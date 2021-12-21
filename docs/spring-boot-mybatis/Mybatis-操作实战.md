```xml
<include refid="userIdWhereSql">
	<property name="table" value="rw"/>
</include>

<sql id="userIdWhereSql">
    <choose>
        <when test="userIds != null">
            ${table}.user_id in
            <foreach collection="userIds" item="item" open="(" close=")" separator=",">
                #{item}
            </foreach>
        </when>
        <otherwise>
            ${table}.user_id=#{userId}
        </otherwise>
    </choose>
</sql>
```

