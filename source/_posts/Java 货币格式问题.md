---
title: Java 货币格式化
---

## 1.通过获取手机locate进行货币格式化 ##

```java
    NumberFormat currencyFormat =  NumberFormat.getCurrencyInstance(locale);
    Double price = Double.parseDouble("122.22222");
    tvPrice.setText(getString(currencyFormat.format(price));
```

很easy不多说了

## 2.通过 ISO 4217 currency code 进行货币格式化 ##

比如说我们获取到了currency code 为 "EUR" or "USD" 我们该怎么根据这个进行格式化呢？

### 方法一：通过currency code 获取 locate，然后同上进行格式化操作 ###

我们写了一个这么个方法：

```java
public static Collection<Locale> getLocalesFromIso4217(String iso4217code) {
    Collection<Locale> returnValue = new LinkedList<Locale>();
    for (Locale locale : NumberFormat.getAvailableLocales()) {
        String code = NumberFormat.getCurrencyInstance(locale).
        getCurrency().getCurrencyCode();
        if (iso4217code.equals(code)) {
            returnValue.add(locale);
        }
    }  
    return returnValue;
}
```
然后打印输出测试一下：
```java
public static void main(String[] args) {
    System.out.println(getLocalesFromIso4217("USD"));
    System.out.println(getLocalesFromIso4217("EUR"));
    for (Locale locale : getLocalesFromIso4217("EUR")) {
        System.out.println(locale + "=>" + NumberFormat.getCurrencyInstance(locale).format(1234));
    }
}
```
结果如下：

```
[en_US, es_US, es_EC, es_PR]
[pt_PT, el_CY, fi_FI, en_MT, sl_SI, ga_IE, fr_BE, es_ES, de_AT, nl_NL, el_GR, it_IT, en_IE, fr_LU, nl_BE, ca_ES, sr_ME, mt_MT, fr_FR, de_DE, de_LU]

pt_PT=>1.234,00 €
el_CY=>€1.234,00
fi_FI=>1 234,00 €
en_MT=>€1,234.00
sl_SI=>€ 1.234
ga_IE=>€1,234.00
fr_BE=>1.234,00 €
es_ES=>1.234,00 €
de_AT=>€ 1.234,00
nl_NL=>€ 1.234,00
el_GR=>1.234,00 €
it_IT=>€ 1.234,00
en_IE=>€1,234.00
fr_LU=>1 234,00 €
nl_BE=>1.234,00 €
ca_ES=>€ 1.234,00
sr_ME=>€ 1.234,00
mt_MT=>€1,234.00
fr_FR=>1 234,00 €
de_DE=>1.234,00 €
de_LU=>1.234,00 €
```
好像和预想的不一样，他喵的竟然返回这么多**locate**，日了狗。而且那个方法还很**耗时**！！！只有另寻他法。

### 方法二：通过Currency 进行格式化 ###

**What is Currency:**
>Represents a currency. Currencies are identified by their ISO 4217 currency codes. Visit the ISO web site for more information, including a table of currency codes.
The class is designed so that there's never more than one Currency instance for any given currency. Therefore, there's no public constructor. You obtain a Currency instance using the getInstance methods.
Users can supersede the Java runtime currency data by creating a properties file named <JAVA_HOME>/lib/currency.properties. The contents of the properties file are key/value pairs of the ISO 3166 country codes and the ISO 4217 currency data respectively. The value part consists of three ISO 4217 values of a currency, i.e., an alphabetic code, a numeric code, and a minor unit. Those three ISO 4217 values are separated by commas. The lines which start with '#'s are considered comment lines. For example,
Sample currency properties
JP=JPZ,999,0
will supersede the currency data for Japan.

自己看，明白了吧！

代码如下：

```java
Currency c = Currency.getInstance("EUR");
NumberFormat currencyFormat = NumberFormat.getCurrencyInstance();
currencyFormat.setMaximumFractionDigits(c.getDefaultFractionDigits());
currencyFormat.setCurrency(c);
```

然后就尽情Format吧!!!






