#### 单个切面执行顺序

```java
Around前
Before
Person{name='小明', nameDesen='null', address='太阳', addressDesen='null', desc='强壮'}
Around后
After
AfterReturning
```

#### 多个切面的执行顺序

````java
高Around前
高Before
中Around前
中Before
低Around前
低Before
Person{name='小明', nameDesen='null', address='太阳', addressDesen='null', desc='强壮'}
低Around后
低After
低AfterReturning
中Around后
中After
中AfterReturning
高Around后
高After
高AfterReturning
````

切面类用@Order(10)注解排序，数值越小，优先级越高，越先执行，越后退出

#### 注解与切面的处理类写法

````java
public class DesensitizationUtils {
    public static void desensitization(Object object) throws Exception {
        if (object == null) {
            return;
        }
        // Collection类型
        if (ClassUtils.isAssignable(object.getClass(), Collection.class)) {
            Collection collection = (Collection) object;
            for (Object tmp : collection) {
                desensitization(tmp);
            }
            // 数组类型
        } else if (object.getClass().isArray()) {
            if (object.getClass().getComponentType().isPrimitive()) {
                return;
            }
            Object[] objects = (Object[]) object;
            for (Object tmp : objects) {
                desensitization(tmp);
            }
            // Map类型
        } else if (ClassUtils.isAssignable(object.getClass(), Map.class)) {
            Map map = (Map) object;
            for (Object tmp : map.values()) {
                desensitization(tmp);
            }
        }
		// 自定义一个注解，标注在类上，表示这个类应该被某个切面处理，这是为了效率以及防止死循环，
        // 如果不对这个添加这个条件作为递归条件的退出，而仅仅只是不断递归遍历实例的字段，可能会遇到
        // 类似Integer这种类，自身还持有Integer类型的字段，导致死循环。
        // 同时利用注解@AopAware这种递归也要求包含的字段的类型也标注@AopAware不能循环引用，否则死循环
        AopAware annotation = object.getClass().getAnnotation(AopAware.class);
        if (annotation == null) {
            return;
        }
        Field[] fields = object.getClass().getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            Object fieldObject= field.get(object);
            if (fieldObject == null || fieldObject.getClass().isPrimitive()) {
                continue;
            }
            // Collection类型
            if (ClassUtils.isAssignable(fieldObject.getClass(), Collection.class)) {
                Collection collection = (Collection) fieldObject;
                for (Object tmp : collection) {
                    desensitization(tmp);
                }
                // 数组类型
            } else if (fieldObject.getClass().isArray()) {
                if (fieldObject.getClass().getComponentType().isPrimitive()) {
                    continue;
                }
                Object[] objects = (Object[]) fieldObject;
                for (Object tmp : objects) {
                    desensitization(tmp);
                }
                // Map类型
            } else if (ClassUtils.isAssignable(fieldObject.getClass(), Map.class)) {
                Map map = (Map) fieldObject;
                for (Object tmp : map.values()) {
                    desensitization(tmp);
                }
            }
            else {
                if (fieldObject.getClass().equals(String.class) ) {
                    // 找到标注在字段上符合要求的字段，可对此做业务逻辑
                    if (!Objects.equals(fieldObject, "") && 
                        field.getAnnotation(Desensitization.class) != null) {
                        try {
                            // 再次写业务逻辑处理方法

                        } catch (Throwable e) {

                            e.printStackTrace();
                        }

                    }
                } else {
                    desensitization(fieldObject);
                }
            }
        }
    }
}
````



