---
title: mybatis与mysql的Json格式映射-使用JSONObject
date: 2020-03-26 14:41:01
tags:
- MyBatis
- MySQL
categories:
- MyBatis
- MySQL
permalink: mybatis-json
---

# mybatis与mysql的Json格式映射-使用JSONObject

JavaBean类

```java
public class ShareClose {
    private Integer id;

    private String tsCode;

    private JSONObject close;
}
```

typeHandler

<!-- more -->

```java
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;
import org.apache.ibatis.type.MappedTypes;
import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import com.alibaba.fastjson.JSONObject;
import org.springframework.context.annotation.Description;


@MappedTypes(JSONObject.class)
@MappedJdbcTypes(JdbcType.LONGVARCHAR)
public class JsonObjectTypeHandler extends BaseTypeHandler<JSONObject> {

    /**
     * 设置非空参数
     * @param ps
     * @param i
     * @param parameter
     * @param jdbcType
     * @throws SQLException
     */
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, JSONObject parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, String.valueOf(parameter.toJSONString()));
    }

    /**
     * 根据列名，获取可以为空的结果
     * @param rs
     * @param columnName
     * @return
     * @throws SQLException
     */
    @Override
    public JSONObject getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String sqlJson = rs.getString(columnName);
        if (null != sqlJson){
            return JSONObject.parseObject(sqlJson);
        }
        return null;
    }

    /**
     * 根据列索引，获取可以为空的结果
     * @param rs
     * @param columnIndex
     * @return
     * @throws SQLException
     */
    @Override
    public JSONObject getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String sqlJson = rs.getString(columnIndex);
        if (null != sqlJson){
            return JSONObject.parseObject(sqlJson);
        }
        return null;
    }

    @Override
    public JSONObject getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String sqlJson = cs.getString(columnIndex);
        if (null != sqlJson){
            return JSONObject.parseObject(sqlJson);
        }
        return null;
    }
}
```

mapper.xml

```xml
<resultMap id="ResultMapWithBLOBs" type="com.ms.quanplat.bean.ShareClose">
    <constructor>
        <idArg column="id" javaType="java.lang.Integer" jdbcType="INTEGER" />
        <arg column="ts_code" javaType="java.lang.String" jdbcType="VARCHAR" />
        <arg column="close" javaType="com.alibaba.fastjson.JSONObject" jdbcType="LONGVARCHAR" typeHandler="com.ms.quanplat.config.handler.JsonObjectTypeHandler"/>
    </constructor>
</resultMap>
```

最后不要忘记在配置文件中配置handler的包路径

```yaml
mybatis:
  type-handlers-package: com.ms.quanplat.config.handler
```

