Mapstruct：
```
实体属性相同、名称不同转换：
@Mappings({ @Mapping(source="grade", target="level") })
属性、常量转换：
@Mapping(target="ordType",constant="3")
```

基本属性与枚举互相转换：
```
自定义CustomMapper转换类，并在mapper接口中引用
@Mapper(uses={DateMapper.class,CustomMapper.class})
public interface OverTimeOrderMapper {
    
    PrepaidGatewayBase converterPrepaidGatewayBase(PayOnlineContext context);
    
    ...
}
```

//自定义mapper转换类
```
public class DateMapper {
    public DateMapper() {
    }

    public Long asLong(Instant date) {
        return date.toEpochMilli();
    }

    public Instant asInstant(Long date) {
        return Instant.ofEpochMilli(date);
    }
}
```

```
public class CustomMapper {
    // 枚举转基础字段
    public Integer asOrderStateInteger(OverTimeOrderState state){
        return state.getCode();
    }
    // 基础字段转枚举
    public OverTimeOrderState asEnumState(Integer state){
        for(OverTimeOrderStateoverTimeOrderState:OverTimeOrderState.values()){
            if(overTimeOrderState.getCode().equals(state)){
                return overTimeOrderState;
            }
        }
    }
}
```