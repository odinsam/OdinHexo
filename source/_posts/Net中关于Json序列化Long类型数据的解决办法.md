title: .Net中关于Json序列化Long类型数据的解决办法
author: OdinSam
tags:
  - .Net Core
  - Json
  - Long类型数据
categories:
  - .Net Core
abbrlink: 4ad7
date: 2021-07-06 12:58:00
---
>在分布式的项目中，我们的数据库Id不能再像以前一样使用int类型自动增长，这时候我们需要一个在网络当中都要能够保持唯一的值，通常情况我们会使用Guid来解决这个问题，但是作为string类型，他并不适合作为主键。尤其是在查询等需要索引操作的时候显得尤为重要。

<!--more-->

##### 1. 这时候我们通常会选择使用雪花Id来解决这个问题，他是一个能在网络当中能够保证唯一的数值number类型的数字，对应在csharp中是long类型。具体雪花Id的原理网上都有，这里直接上生成雪花Id的代码：

```csharp
public class OdinSnowFlake : IOdinSnowFlake
{
    // 开始时间截((new DateTime(2020, 1, 1, 0, 0, 0, DateTimeKind.Utc)-Jan1st1970).TotalMilliseconds)
    private readonly long twepoch;
    // 机器id所占的位数
    private const int workerIdBits = 5;
    // 数据标识id所占的位数
    private const int datacenterIdBits = 5;
    // 支持的最大机器id，结果是31 (这个移位算法可以很快的计算出几位二进制数所能表示的最大十进制数)
    private const long maxWorkerId = -1L ^ (-1L << workerIdBits);
    // 支持的最大数据标识id，结果是31
    private const long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);
    // 序列在id中占的位数
    private const int sequenceBits = 12;
    // 数据标识id向左移17位(12+5)
    private const int datacenterIdShift = sequenceBits + workerIdBits;
    // 机器ID向左移12位
    private const int workerIdShift = sequenceBits;
    // 时间截向左移22位(5+5+12)
    private const int timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;
    // 生成序列的掩码，这里为4095 (0b111111111111=0xfff=4095)
    private const long sequenceMask = -1L ^ (-1L << sequenceBits);
    // 数据中心ID(0~31)
    public long datacenterId { get; private set; }
    // 工作机器ID(0~31)
    public long workerId { get; private set; }
    // 毫秒内序列(0~4095)
    public long sequence { get; private set; }
    // 上次生成ID的时间截
    public long lastTimestamp { get; private set; }
    private static Dictionary<long, long> dicContainer = null;


    /// <summary>
    /// 雪花ID
    /// </summary>
    /// <param name="datacenterId">数据中心ID</param>
    /// <param name="workerId">工作机器ID</param>
    public OdinSnowFlake(long datacenterId, long workerId)
    {
        this.twepoch = (long)((new DateTime(2020, 1, 1, 0, 0, 0, DateTimeKind.Utc) - Jan1st1970).TotalMilliseconds);
        if (datacenterId > maxDatacenterId || datacenterId < 0)
        {
            throw new Exception(string.Format("datacenter Id can't be greater than {0} or less than 0", maxDatacenterId));
        }
        if (workerId > maxWorkerId || workerId < 0)
        {
            throw new Exception(string.Format("worker Id can't be greater than {0} or less than 0", maxWorkerId));
        }
        this.workerId = workerId;
        this.datacenterId = datacenterId;
        this.sequence = 0L;
        this.lastTimestamp = -1L;
        if (dicContainer == null)
            dicContainer = new Dictionary<long, long>();
    }

    public void InitDic()
    {
        if (dicContainer == null)
            dicContainer = new Dictionary<long, long>();
    }

    public void ClearDic()
    {
        if (dicContainer != null)
            dicContainer.Clear();
    }

    /// <summary>
    /// 获得下一个ID
    /// </summary>
    /// <returns></returns>
    public long NextId()
    {
        lock (this)
        {
            long timestamp = GetCurrentTimestamp();
            if (timestamp > lastTimestamp) //时间戳改变，毫秒内序列重置
            {
                sequence = 0L;
            }
            else if (timestamp == lastTimestamp) //如果是同一时间生成的，则进行毫秒内序列
            {
                sequence = (sequence + 1) & sequenceMask;
                if (sequence == 0) //毫秒内序列溢出
                {
                    timestamp = GetNextTimestamp(lastTimestamp); //阻塞到下一个毫秒,获得新的时间戳
                }
            }
            else //当前时间小于上一次ID生成的时间戳，证明系统时钟被回拨，此时需要做回拨处理
            {
                sequence = (sequence + 1) & sequenceMask;
                if (sequence > 0)
                {
                    timestamp = lastTimestamp; //停留在最后一次时间戳上，等待系统时间追上后即完全度过了时钟回拨问题。
                }
                else //毫秒内序列溢出
                {
                    timestamp = lastTimestamp + 1; //直接进位到下一个毫秒
                }
                //throw new Exception(string.Format("Clock moved backwards.  Refusing to generate id for {0} milliseconds", lastTimestamp - timestamp));
            }

            lastTimestamp = timestamp; //上次生成ID的时间截

            //移位并通过或运算拼到一起组成64位的ID
            var id = ((timestamp - twepoch) << timestampLeftShift) |
                (datacenterId << datacenterIdShift) |
                (workerId << workerIdShift) |
                sequence;
            if (!dicContainer.ContainsKey(id))
            {
                dicContainer.Add(id, id);
                return id;
            }
            else
            {
                Thread.Sleep(1);
                return NextId();
            }
        }
    }

    /// <summary>
    /// 解析雪花ID
    /// </summary>
    /// <returns></returns>
    public string AnalyzeId(long Id)
    {
        StringBuilder sb = new StringBuilder();
        var timestamp = (Id >> timestampLeftShift);
        var time = Jan1st1970.AddMilliseconds(timestamp + twepoch);
        sb.Append(time.ToLocalTime().ToString("yyyy-MM-dd HH:mm:ss:fff"));
        var datacenterId = (Id ^ (timestamp << timestampLeftShift)) >> datacenterIdShift;
        sb.Append("_" + datacenterId);
        var workerId = (Id ^ ((timestamp << timestampLeftShift) | (datacenterId << datacenterIdShift))) >> workerIdShift;
        sb.Append("_" + workerId);
        var sequence = Id & sequenceMask;
        sb.Append("_" + sequence);
        return sb.ToString();
    }

    /// <summary>
    /// 阻塞到下一个毫秒，直到获得新的时间戳
    /// </summary>
    /// <param name="lastTimestamp">上次生成ID的时间截</param>
    /// <returns>当前时间戳</returns>
    private static long GetNextTimestamp(long lastTimestamp)
    {
        long timestamp = GetCurrentTimestamp();
        while (timestamp <= lastTimestamp)
        {
            timestamp = GetCurrentTimestamp();
        }
        return timestamp;
    }

    /// <summary>
    /// 获取当前时间戳
    /// </summary>
    /// <returns></returns>
    private static long GetCurrentTimestamp()
    {
        return (long)(DateTime.UtcNow - Jan1st1970).TotalMilliseconds;
    }

    private static readonly DateTime Jan1st1970 = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);

}
```

#### 代码解释：

1. 提供了构造函数，其中datacenterId为当前数据中心Id:一般从1开始。workerId是机器Id,需要注意的是在网络节点当中的服务器，这个Id不能重复

2. 代码的 NextId() 方法将会生成一个 18位长的long类型的雪花Id。

3. AnalyzeId() 方法可以简单的解析一个long的数值是不是符合雪花Id的规范。这个解析不精准，只能判断格式大致是否正确具体解析规则可以看代码。

4. 有了这个Id，我们通常可以开心的在代码当中以application/json格式返回一个对象，比如

![代码解释](/images/4ad7/code.png)

输出的结果是

![输出结果](/images/4ad7/result.png)

##### <font color='red'>这是因为 JavaScript 数值精度是32位，如果整数数度超过32位，就会被当作浮点数处理。换句话说，如果从服务端生成的JSON，某个值是64位整数，传到前端JavaScript，再传回服务端，不做任何运算，都可能出现失真。</font>

解决问题的办法：将long作为string类型序列化输出 代码如下：

```csharp
public class Stu
{
    [JsonConverter(typeof(JsonConverterLong))]
    public long id { get; set; }
    public string name { get; set; }
    public int age { get; set; }
}
```

JsonConverterLong 类

```csharp
public class JsonConverterLong : JsonConverter
{
    /// <summary>
    /// 是否可以转换
    /// </summary>
    /// <param name="objectType"></param>
    /// <returns></returns>
    public override bool CanConvert(Type objectType)
    {
        return true;
    }

    /// <summary>
    /// 读json
    /// </summary>
    /// <param name="reader"></param>
    /// <param name="objectType"></param>
    /// <param name="existingValue"></param>
    /// <param name="serializer"></param>
    /// <returns></returns>
    public override object ReadJson(JsonReader reader, Type objectType, object existingValue, JsonSerializer serializer)
    {
        if ((reader.ValueType == null || reader.ValueType == typeof(long?)) && reader.Value == null)
        {
            return null;
        }
        else
        {
            long.TryParse(reader.Value != null ? reader.Value.ToString() : "", out long value);
            return value;
        }
    }

    /// <summary>
    /// 写json
    /// </summary>
    /// <param name="writer"></param>
    /// <param name="value"></param>
    /// <param name="serializer"></param>
    public override void WriteJson(JsonWriter writer, object value, JsonSerializer serializer)
    {
        if (value == null)
            writer.WriteValue(value);
        else
            writer.WriteValue(value + "");
    }
}
```


































```