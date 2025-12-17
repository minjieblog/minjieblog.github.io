---
title: "黑马点评项目笔记"
date: 2025-12-17T21:01:53+08:00
draft: false
tags: ["Redis", "Spring Boot", "微服务", "缓存", "分布式"]
categories: ["技术笔记-J"]
summary: "黑马点评项目学习笔记，涵盖 Redis 缓存、分布式锁、消息队列等核心技术实践"
description: |
  "记录黑马点评项目的学习过程，包括 Redis 的多种应用场景、缓存穿透/击穿/雪崩解决方案、分布式锁实现、UV 统计、好友关注等功能的技术实现细节"
comments: true
---

## 0.前言

### 功能概要

[![image.png](https://i.postimg.cc/XYQS4zXV/image.png)](https://postimg.cc/DSJYc5qD)

### 数据库设计

#### 1.  tb_blog (博客/探店笔记表)

**功能**: 存储用户发布的探店笔记内容

| 字段        | 类型          | 说明                              |
| ----------- | ------------- | --------------------------------- |
| id          | bigint(20)    | 主键，自增                        |
| shop_id     | bigint(20)    | 关联的商户ID                      |
| user_id     | bigint(20)    | 发布用户ID                        |
| title       | varchar(255)  | 笔记标题                          |
| images      | varchar(2048) | 图片路径，多张用逗号分隔，最多9张 |
| content     | varchar(2048) | 文字描述内容                      |
| liked       | int(8)        | 点赞数量                          |
| comments    | int(8)        | 评论数量                          |
| create_time | timestamp     | 创建时间                          |
| update_time | timestamp     | 更新时间                          |

**示例数据**: 包含美食探店笔记，如"无尽浪漫的夜晚"、"人均30💰杭州港式茶餐厅"等

#### 2. tb_blog_comments (博客评论表)

**功能**: 存储博客的评论信息，支持多级评论

| 字段        | 类型         | 说明                            |
| ----------- | ------------ | ------------------------------- |
| id          | bigint(20)   | 主键，自增                      |
| user_id     | bigint(20)   | 评论用户ID                      |
| blog_id     | bigint(20)   | 关联的博客ID                    |
| parent_id   | bigint(20)   | 父评论ID，一级评论为0           |
| answer_id   | bigint(20)   | 回复的评论ID                    |
| content     | varchar(255) | 评论内容                        |
| liked       | int(8)       | 点赞数                          |
| status      | tinyint(1)   | 状态: 0正常, 1被举报, 2禁止查看 |
| create_time | timestamp    | 创建时间                        |
| update_time | timestamp    | 更新时间                        |

**设计亮点**: 通过parent_id和answer_id支持多层级的评论回复

#### 3. tb_follow (关注关系表)

**功能**: 记录用户之间的关注关系

| 字段           | 类型       | 说明           |
| -------------- | ---------- | -------------- |
| id             | bigint(20) | 主键，自增     |
| user_id        | bigint(20) | 用户ID         |
| follow_user_id | bigint(20) | 被关注的用户ID |
| create_time    | timestamp  | 创建时间       |

**用途**: 实现社交功能，构建用户关系网络

#### 4. tb_shop (商铺表)

**功能**: 存储商铺的基本信息

| 字段        | 类型          | 说明                     |
| ----------- | ------------- | ------------------------ |
| id          | bigint(20)    | 主键，自增               |
| name        | varchar(128)  | 商铺名称                 |
| type_id     | bigint(20)    | 商铺类型ID               |
| images      | varchar(1024) | 商铺图片，多张用逗号分隔 |
| area        | varchar(128)  | 商圈，如"陆家嘴"         |
| address     | varchar(255)  | 详细地址                 |
| x           | double        | 经度                     |
| y           | double        | 纬度                     |
| avg_price   | bigint(10)    | 人均价格（整数）         |
| sold        | int(10)       | 销量                     |
| comments    | int(10)       | 评论数量                 |
| score       | int(2)        | 评分(1-5分，乘10保存)    |
| open_hours  | varchar(32)   | 营业时间                 |
| create_time | timestamp     | 创建时间                 |
| update_time | timestamp     | 更新时间                 |

**示例商铺**: 包含103茶餐厅、海底捞火锅、开乐迪KTV等14家商铺

#### 5. tb_shop_type (商铺类型表)

**功能**: 商铺分类管理

| 字段        | 类型         | 说明       |
| ----------- | ------------ | ---------- |
| id          | bigint(20)   | 主键，自增 |
| name        | varchar(32)  | 类型名称   |
| icon        | varchar(255) | 图标路径   |
| sort        | int(3)       | 排序       |
| create_time | timestamp    | 创建时间   |
| update_time | timestamp    | 更新时间   |

**分类**: 美食、KTV、丽人美发、健身运动、按摩足疗、美容SPA、亲子游乐、酒吧、轰趴馆、美睫美甲

#### 6. tb_user (用户表)

**功能**: 存储用户基本信息

| 字段        | 类型         | 说明               |
| ----------- | ------------ | ------------------ |
| id          | bigint(20)   | 主键，自增         |
| phone       | varchar(11)  | 手机号（唯一索引） |
| password    | varchar(128) | 加密密码           |
| nick_name   | varchar(32)  | 昵称               |
| icon        | varchar(255) | 头像路径           |
| create_time | timestamp    | 创建时间           |
| update_time | timestamp    | 更新时间           |

**安全设计**: 密码加密存储，手机号唯一

#### 7. tb_user_info (用户详细信息表)

**功能**: 存储用户的扩展信息

| 字段        | 类型         | 说明             |
| ----------- | ------------ | ---------------- |
| user_id     | bigint(20)   | 主键，关联用户ID |
| city        | varchar(64)  | 城市             |
| introduce   | varchar(128) | 个人介绍         |
| fans        | int(8)       | 粉丝数           |
| followee    | int(8)       | 关注数           |
| gender      | tinyint(1)   | 性别: 0男, 1女   |
| birthday    | date         | 生日             |
| credits     | int(8)       | 积分             |
| level       | tinyint(1)   | 会员等级(0-9)    |
| create_time | timestamp    | 创建时间         |
| update_time | timestamp    | 更新时间         |

#### 8. tb_voucher (代金券表)

**功能**: 管理商铺的优惠券信息

| 字段         | 类型          | 说明                      |
| ------------ | ------------- | ------------------------- |
| id           | bigint(20)    | 主键，自增                |
| shop_id      | bigint(20)    | 商铺ID                    |
| title        | varchar(255)  | 券标题                    |
| sub_title    | varchar(255)  | 副标题                    |
| rules        | varchar(1024) | 使用规则                  |
| pay_value    | bigint(10)    | 支付金额（分）            |
| actual_value | bigint(10)    | 抵扣金额（分）            |
| type         | tinyint(1)    | 类型: 0普通券, 1秒杀券    |
| status       | tinyint(1)    | 状态: 1上架, 2下架, 3过期 |
| create_time  | timestamp     | 创建时间                  |
| update_time  | timestamp     | 更新时间                  |

#### 9. tb_seckill_voucher (秒杀券表)

**功能**: 存储秒杀券的特殊信息

| 字段        | 类型       | 说明           |
| ----------- | ---------- | -------------- |
| voucher_id  | bigint(20) | 主键，关联券ID |
| stock       | int(8)     | 库存数量       |
| begin_time  | timestamp  | 生效时间       |
| end_time    | timestamp  | 失效时间       |
| create_time | timestamp  | 创建时间       |
| update_time | timestamp  | 更新时间       |

**特点**: 与tb_voucher是一对一关系，专门处理秒杀场景

#### 10. tb_voucher_order (优惠券订单表)

**功能**: 记录用户购买优惠券的订单

| 字段        | 类型       | 说明                                                         |
| ----------- | ---------- | ------------------------------------------------------------ |
| id          | bigint(20) | 主键（非自增）                                               |
| user_id     | bigint(20) | 用户ID                                                       |
| voucher_id  | bigint(20) | 优惠券ID                                                     |
| pay_type    | tinyint(1) | 支付方式: 1余额, 2支付宝, 3微信                              |
| status      | tinyint(1) | 订单状态: 1未支付, 2已支付, 3已核销, 4已取消, 5退款中, 6已退款 |
| create_time | timestamp  | 下单时间                                                     |
| pay_time    | timestamp  | 支付时间                                                     |
| use_time    | timestamp  | 核销时间                                                     |
| refund_time | timestamp  | 退款时间                                                     |
| update_time | timestamp  | 更新时间                                                     |

**设计亮点**: 完整的订单状态流转，支持退款流程

#### 11. tb_sign (签到表)

**功能**: 记录用户的签到信息

| 字段      | 类型       | 说明       |
| --------- | ---------- | ---------- |
| id        | bigint(20) | 主键，自增 |
| user_id   | bigint(20) | 用户ID     |
| year      | year       | 签到年份   |
| month     | tinyint(2) | 签到月份   |
| date      | date       | 签到日期   |
| is_backup | tinyint(1) | 是否补签   |

**用途**: 用户激励体系，支持补签功能

## 1.短信登录

### 基于session实现登录

#### 1.登录流程

![ping-mu-jie-tu-2025-12-16-183317.png](https://i.postimg.cc/44c0Jb5T/ping-mu-jie-tu-2025-12-16-183317.png)

#### 2.代码实现

**发送短信验证码**

```java
@Slf4j
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
    @Override
    public Result sendcode(String phone, HttpSession session){
        //1.校验手机号,utils的工具包
        if(RegexUtils.isPhoneInvalid(phone)){
            //2.如果不符合，返回错误信息
            return Result.fail("手机号格式错误！");
        }

        //3.符合，生成验证码,一个java的工具包
        String code = RandomUtil.randomNumbers(6);

        //4.保存验证码道session
        session.setAttribute("code",code);

        //5.发送验证码,需要调用第三方的短信平台，比如说阿里云
        //TODO
        log.debug("发送短信验证码成功，验证码：{}",code);

        //返回ok
        return Result.ok(code);
    }
}
```

**短信验证码验证和登录**

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session){
    //1.校验手机号
    String phone = loginForm.getPhone();
    if(RegexUtils.isPhoneInvalid(phone)){
        return Result.fail("手机号格式错误！");
    }

    //2.校验验证码
    Object cacheCode = session.getAttribute("code");
    String code = loginForm.getCode();
    if(cacheCode==null || !cacheCode.toString().equals(code)){
        //3.不一致报错
        return Result.fail("验证码错误");
    }

    //4.一致，根据手机号查询用户,select * from tb_user where phone = ?
    User user = query().eq("phone", phone).one();

    //5.判断用户是否存在
    if(user==null){
        //6.不存在，创建用户并保存,只需要填充phone和nickname字段即可
        user = createUserWithPhone(String phone);
    }

    //7.保存用户信息到session中
    session.setAttribute("user",user);
    
    return Result.ok();
}
private  User createUserWithPhone(String phone){
    //1.创建用户
    User user = new User();
    user.setPhone(phone);
    user.setNickName(USER_NICK_NAME_PREFIX + RandomUtil.randomString(10));

    //2.保存用户
    save(user);
    return user;
}
```

**校验登录状态**

```java
/*
	在utils中添加LoginInterceptor.java作为拦截器
*/
public class LoginInterceptor implements HandlerInterceptor {
    //预拦截
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //1.获取session
        HttpSession session = request.getSession();
        //2.获取session中的user
        Object user = session.getAttribute("user");
        //3.判断用户是否在存在
        if(user==null){
            //4.不存在，返回401状态码
            response.setStatus(401);
            return false;
        }
        //5.存在，保存用户信息到ThreadLocal
        UserHolder.saveUser((UserDTO) user);
        //6.放行
        return true;
    }

    //渲染之前的拦截
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        //移除用户
        UserHolder.removeUser();
    }
}


/*
	在config中添加MvcConfig.java
*/
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns(
                        "/user/code"
                        , "/user/login"
                        , "/blog/hot"
                        , "/shop/**"
                        , "/shop-type/**"
                        , "/upload/**"
                        , "/voucher/**"
                );
    }
}

/*
	controller拿到用户登录的信息，通过这个ThreadLocal	
*/
@GetMapping("/me")
public Result me(){
    // 获取当前登录的用户并返回
    UserDTO user = UserHolder.getUser();
    return Result.ok(user);
}
```

**隐藏用户信息**

```java
/*
	1.需要在登录的时候，将传入session的user更改成userDTO
	2.更改UserHolder中的所有的相关信息和拦截器调用信息为userDTO
	3.更改me接口，return一个UserDTO信息
*/
//登录时保存用户信息到session中
session.setAttribute("user", BeanUtil.copyProperties(user, UserDTO.class));
```

### 集群的session共享问题

![image.png](https://i.postimg.cc/WzTP9xXV/image.png)

### 基于Redis实现共享session问题

#### 1.Redis字段选择

**设计key的要求**

- *唯一性*
- *便携带*

[![image.png](https://i.postimg.cc/J47y6z6Z/image.png)](https://postimg.cc/V5TL5mck)

#### 2.Redis实现验证码发送登录问题

![image.png](https://i.postimg.cc/8k6wptNX/image.png)

#### 3.Redis实现校验登录功能

[![image.png](https://i.postimg.cc/gjCT0TTG/image.png)](https://postimg.cc/MfYDPdwL)

#### 4.代码实现

**发送短信验证码**

```java
 @Resource
private StringRedisTemplate stringRedisTemplate;

@Override
public Result sendcode(String phone, HttpSession session){
    //1.校验手机号,utils的工具包
    if(RegexUtils.isPhoneInvalid(phone)){
        //2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }

    //3.符合，生成验证码,一个java的工具包
    String code = RandomUtil.randomNumbers(6);

    //4.保存验证码到redis
    stringRedisTemplate.opsForValue().set(LOGIN_CODE_KEY+phone,code,LOGIN_CODE_TTL, TimeUnit.MINUTES);

    //5.发送验证码,需要调用第三方的短信平台，比如说阿里云
    log.debug("发送短信验证码成功，验证码：{}",code);

    //返回ok
    return Result.ok(code);
}
```



**短信验证码验证和登录**

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session){
    //1.校验手机号
    String phone = loginForm.getPhone();
    if(RegexUtils.isPhoneInvalid(phone)){
        return Result.fail("手机号格式错误！");
    }

    //2.从redis中获取验证码并进行校验
    String cacheCode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY+phone);
    String code = loginForm.getCode();
    if(cacheCode==null || !cacheCode.equals(code)){
        //3.不一致报错
        return Result.fail("验证码错误");
    }

    //4.一致，根据手机号查询用户,select * from tb_user where phone = ?
    User user = query().eq("phone", phone).one();

    //5.判断用户是否存在
    if(user==null){
        //6.不存在，创建用户并保存,只需要填充phone和nickname字段即可
        user = createUserWithPhone(phone);
    }

    //7.保存用户信息到redis
    //7.1随机生成token,作为登录令牌
    String token = UUID.randomUUID().toString();
    //7.2将user对象转为hash存储
    UserDTO userDTO = BeanUtil.copyProperties(user,UserDTO.class);
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO,new HashMap<>(),
                //解决id字段是整型而不是字符串类型
                CopyOptions.create().setIgnoreNullValue(true).setFieldValueEditor((fieldName,fieldValue)->fieldValue.toString())
        );
    //7.3存储
    String tokenKey = LOGIN_USER_KEY+token;
    stringRedisTemplate.opsForHash().putAll(tokenKey,userMap);   //添加hash对象
    stringRedisTemplate.expire(tokenKey,LOGIN_USER_TTL, TimeUnit.MINUTES);

    //8.返回token
    return Result.ok(token);
```



**校验登录状态**

- 需要在`MvcConfig`中导入`RedisTemplate`
- *使用拦截器是为了解决redis像session一样只要有操作就会自动延长过期时间*

```java
private StringRedisTemplate stringRedisTemplate;

public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
    this.stringRedisTemplate = stringRedisTemplate;
}

//预拦截
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    //1.获取请求头中的token
    String token = request.getHeader("authorization");
    if(StrUtil.isBlank(token)){
        response.setStatus(401);
        return false;
    }

    //2.基于token获取redis中的用户
    String key = RedisConstants.LOGIN_USER_KEY + token;
    Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(key);

    //3.判断用户是否在存在
    if(userMap.isEmpty()){
        //4.不存在，返回401状态码
        response.setStatus(401);
        return false;
    }
    //5.将查询到的hash数据转为UserDTO对象
    UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap,new UserDTO(),false);

    //6.存在，保存用户信息到ThreadLocal
    UserHolder.saveUser(userDTO);

    //7.刷新token的有效期
    stringRedisTemplate.expire(key,RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);

    //8.放行
    return true;
}


/*
	此时的MVCCONFIG需要更新
*/
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor(stringRedisTemplate))
                .excludePathPatterns(
                        "/user/code"
                        , "/user/login"
                        , "/blog/hot"
                        , "/shop/**"
                        , "/shop-type/**"
                        , "/upload/**"
                        , "/voucher/**"
                );
    }
}
```

**登录拦截器的优化**

- *如果用户访问的全都不是在拦截器的页面，token仍然会失效*

```java
//具体做法：拷贝一份新的RefreshTokenInterceptor，并重写一下LoginInterceptor

@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    //1.判断是否需要进行拦截（ThreadLocal中是否有用户）
    if(UserHolder.getUser() == null){
        //没有，需要进行拦截
        response.setStatus(401);
        //拦截
        return false;
    }
    //有用户，进行放行操作
    return true;
}


//需要更新一下MvcConfig
@Configuration
public class MvcConfig implements WebMvcConfigurer {
@Resource
private StringRedisTemplate stringRedisTemplate;

@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginInterceptor())
            .excludePathPatterns(
                    "/user/code"
                    , "/user/login"
                    , "/blog/hot"
                    , "/shop/**"
                    , "/shop-type/**"
                    , "/upload/**"
                    , "/voucher/**"
            ).order(1);
    registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0);
}
}	
```

## 2.商户查询缓存

## 3.优惠券秒杀

### 分布式锁

## 4.达人探店

## 5.好友关注

## 6.附近商铺搜索

## 7.用户签到

## 8.UV统计



