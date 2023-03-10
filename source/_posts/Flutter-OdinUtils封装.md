title: Flutter - OdinUtils封装
author: OdinSam
tags:
  - OdinUtils
  - Flutter
categories:
  - Flutter
abbrlink: eff3
date: 2022-12-03 17:00:00
---
> Flutter - OdinUtils封装 完整代码可以在 [GitHub](https://github.com/odinsam/OdinPlugin/tree/master/odinUtils)

<!--more-->

#### Flutter - OdinUtils封装 ![plateform](https://img.shields.io/badge/Plateform-ios%7Candroid%7Cwindws%7Clinux-greenyellow) ![version](https://img.shields.io/badge/pub.dev-1.0.1-greenyellow)
```yaml 
dependencies:
  # dart linq function
  flinq: ^2.0.2
  # dart crypto
  crypto: ^3.0.2 
```

1. OdinUtils 扩展方法包括内容如下:

|扩展类型|方法名称|方法描述|
|--|--|--|
|String|	compareIgnoreCase|	忽略大小写比较|
|String|	isChinaCardId|	判断中国身份号码格式|
|String|	isChinaMobile|	判断中国移动电话号码格式|
|String|	isIpAddress|	判断Ip地址格式|
|String|	isEmail|	判断邮箱地址格式|
|String|	isUrl|	判断url地址格式|
|String|	toMd5|	string进行md5加密|
|String|	toSHA1|	string进行SHA1加密|
|String|	toCharList|	转CharList List类型|
|int|	unixTimerToTimer|	UnixTimer时间戳转DateTime|
|DatTime|	toUnixTime|	DateTime转UnixTimer时间戳|
|DatTime|	减运算符 - 运算符重载|	时间差|
|DatTime|	isLeapYear|	是否是闰年|
|Random|	odinNextInt|	随机 A-B 之间的int随机数|
|Random|	odinNextDouble|	随机 A-B 之间的double随机数|
|ParamsSignUtils|	urlAddSign|	url添加sign签名|
|ParamsSignUtils|	validateSign|	验证url sign签名 是否正确|
|ParamsSignUtils|	createUrlSign|	创建 sign签名 是否正确|

2. OdinUtils Utils方法包括内容如下:

|工具类类型|	方法名称	|方法描述|
|--|--|--|
|String|	OdinStringUtils.generationCode|	按长度生成对应的字符串|
|String|	OdinStringUtils.generationLimpidCode|	按长度生成对应的字符串，不包含 0 o 1 I 等容易混淆的之母和数字|
|OdinAlgorithm|	OdinAlgorithm.getRandomListByWeight|	按权重返回对应需要个数的数组|
|OdinRandomUtils|	OdinRandomUtils.createRandom|	创建random随机对象|
|OdinTransformUtils|	OdinTransformUtils.convertNumberToChineseMoneyWords|	数字转人民币大写|
|OdinTransformUtils|	OdinTransformUtils.convertNumberToChineseNumber|	数字转中文大写|
|OdinTransformUtils|	OdinTransformUtils.convertDateToChineseDate|	日期转中文大写|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseCalendarHoliday|	计算中国农历节日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).weekDayHoliday|	按某月第几周第几日计算的节日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).dateHoliday|	按公历日计算的节日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).weekDayStr	|周几的字符|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).dateString	|公历日期中文表示法 如一九九七年七月一日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).isLeapYear	|当前是否公历闰年|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseConstellation	|28星宿计算|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseHour	|时辰|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).isChineseLeapMonth	|是否闰月|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).isChineseLeapYear	|当年是否有闰月|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseDay	|农历日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseDayString	|农历日中文表示|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseMonth	|农历的月份|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseMonthString	|农历月份字符串|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseYear	|取农历年份|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseYearString	|中文纪年|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseDateString	|取农历日期表示法：农历一九九七年正月初五|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).chineseTwentyFourDay	|当前日期后一个最近节气|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).constellation	计算指定日期的星座序号|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).animal	|计算属相的索引，注意虽然属相是以农历年来区别的，但是目前在实际使用中是按公历来计算的 鼠年为1,其它类推|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).animalString	|取属相字符串|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).ganZhiYearString	|取农历年的干支表示法如 乙丑年|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).ganZhiMonthString	|取干支的月表示字符串，注意农历的闰月不记干支|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).ganZhiDayString	|取干支日表示法|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).ganZhiDateString	|取当前日期的干支表示法如 甲子年乙丑月丙庚日|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).nextDay	|取下一天|
|OdinChineseCalendar|	OdinChineseCalendar(Date类型).pervDay	|取前一天|


3. test
```dart
test('createUrlSign',(){
    var url = "name=odinsam&age=19";
    var signUrl = ParamsSignUtils();
    var newUrl = signUrl.urlAddSign(url, "abc");
    print(newUrl);
  });

  test('validateUrlSign',(){
    var url = "age=19&name=odinsam&sign=34fa599226a44fc7f2431c78a6b15393";
    var signUrl = ParamsSignUtils();
    var flag = signUrl.validateSign(url, "abc");
    print("验证结果:$flag");
    expect(flag, true);
  });

  test('paramsSign',(){
    var kv = SplayTreeMap();
    kv.addAll({"b":"b"});
    kv.addAll({"a":"a"});
    for(var k in kv.entries){
      print("key:${k.key}\tvalue:${k.value}");
    }
    print(kv['c']);
  });

  test('OdinChineseCalendar',(){
    var str = OdinChineseCalendar(DateTime.now()).chineseCalendarHoliday();
    print(str);
  });

  test('transformChineseDate',(){
    var date = DateTime.now().toString().split(' ')[0];
    var str = OdinTransformUtils.convertDateToChineseDate(date);
    print(str);
  });

  test('transformChineseNumber',(){
    var str = OdinTransformUtils.convertNumberToChineseNumber("100002001.32");
    print(str);
  });

  test('transformMoneyWork',(){
    var str = OdinTransformUtils.convertNumberToChineseMoneyWords("30003.3275");
    print(str);
  });

  test('timer',(){
    var dt = OdinChineseCalendar(DateTime.now());
    print(dt.dateString());
    print(dt.ganZhiDateString());
  });

  test('listSum',(){
    var lst = <Map<Stu, int>>[
      {Stu("odinsam1",120):6},{Stu("odinsam2",220):30},{Stu("odinsam3",120):130},{Stu("odinsam4",420):834}
    ];
    var i1=0,i2=0,i3=0,i4=0;
    for(var i =0;i<100;i++){
      var wlst = OdinAlgorithm.getRandomListByWeight(lst, 1);
      if(wlst[0].keys.first.name=="odinsam1"){
        i1+=1;
      }
      if(wlst[0].keys.first.name=="odinsam2"){
        i2+=1;
      }
      if(wlst[0].keys.first.name=="odinsam3"){
        i3+=1;
      }
      if(wlst[0].keys.first.name=="odinsam4"){
        i4+=1;
      }
    }
    print("1:${i1}\t\t2:${i2}\t\t3:${i3}\t\t4:${i4}");
  });

  test('stringUtils',(){
    String str="abc";
    expect(str.compareIgnoreCase("ABC"), true);
    expect(str.compareIgnoreCase("ABC",ignoreCase: false), false);
  });

  /// 判断中国身份号码格式
  test('stringRegex-中国身份号码格式',(){
    String str="62010419820729029x";
    expect(str.isChinaCardId(), true);
  });

  /// 判断中国移动电话号码格式
  test('stringRegex-中国移动电话号码格式',(){
    String str="17377777777";
    expect(str.isChinaMobile(), true);
  });

  /// 判断邮箱地址格式
  test('stringRegex-邮箱地址格式',(){
    String str="123456@qq.com";
    expect(str.isEmail(), true);
  });

  /// 判断url地址格式
  test('stringRegex-url地址格式',(){
    String str="https://www.baidu.com";
    expect(str.isUrl(), true);
  });

  /// 判断Ip地址格式
  test('stringRegex-Ip地址格式',(){
    String str="127.0.0.1";
    expect(str.isIpAddress(), true);
  });

  /// string进行md5加密
  test('stringMd5',(){
    var str = "odinsam";
    expect(str.toMd5(), "6f61c3e668ff2fc275895b843044829c");
    expect(str.toMd5(upperCase: true), "6F61C3E668FF2FC275895B843044829C");
  });

  /// string进行SHA1加密
  test('stringSHA1',(){
    var str = "odinsam";
    expect(str.toSHA1(), "6d07eb5c4263173417428f226655438d346471f9");
    expect(str.toSHA1(upperCase: true), "6D07EB5C4263173417428F226655438D346471F9");
  });

  /// UnixTimer时间戳转DateTime  DateTime转UnixTimer时间戳
  test('time',(){
    var dt = DateTime.now();
    int unixTime = dt.toUnixTime();
    print(dt.toString());
    print(unixTime);
    print(unixTime.unixTimerToTimer());
  });

  /// 按长度生成对应的字符串
  test('generationCode',(){
    var str = OdinStringUtils.generationCode(4);
    print(str);
    expect(str.length, 4);
  });

  /// 按长度生成对应的字符串，不包含 0 o 1 I 等容易混淆的之母和数字
  test('generationLimpidCode',(){
    var str = OdinStringUtils.generationLimpidCode(6);
    print(str);
    expect(str.length, 6);
  });

  test('$MethodChannelOdinutils is the default instance', () {
    expect(initialPlatform, isInstanceOf<MethodChannelOdinutils>());
  });

  test('getPlatformVersion', () async {
    Odinutils odinutilsPlugin = Odinutils();
    MockOdinutilsPlatform fakePlatform = MockOdinutilsPlatform();
    OdinutilsPlatform.instance = fakePlatform;

    expect(await odinutilsPlugin.getPlatformVersion(), '42');
  });
```

#### Getting Started
pub.dev发布地址：[odinSam-Flutter - OdinUtils 工具类封装](https://pub.dev/packages/odinutils)













