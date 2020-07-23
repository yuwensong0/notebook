spring的BeanUtils的copyProperties不会进行复杂类型的转换，比如String到int，但是apache的可以

spring的BeanUtils的copyProperties可以对非public修饰的pojo进行转换，但是apache的不可以，必须都是public

apache的BeanUtils的copyProperties属性相同，类型不同，没有转换器无法转换，会报错

apache的BeanUtils的copyProperties属性相同，数组元素的类型不同，没有转换器无法转换，会报错类型相同可以正常赋值

属性是对象，copy后共享同一实例，是引用复制。比如复制后的数组两个对象引用的是同一个。

java.utils.Date默认无法转换

BigDecimal可以由合法的字符串转换，但是如果字符串是null会报错

可以自定义添加转换器：

```java
class Myconvert implements Converter{
    @Override
    public Object convert(Class type, Object value) {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        Date parse = null;
        try {
            parse = simpleDateFormat.parse((String) value);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return parse ;
    }
}

A a = new A();
ConvertUtils.register(new Myconvert(), Date.class);
BeanUtils.setProperty(a, "birthday", "2019-12-12");
```