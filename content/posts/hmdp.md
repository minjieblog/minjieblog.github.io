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

### 什么是缓存

**缓存的级别**

[![image.png](https://i.postimg.cc/wTMhxKZY/image.png)](https://postimg.cc/crqKTzdF)

**缓存的优缺点**

[![image.png](https://i.postimg.cc/g2RLrGKg/image.png)](https://postimg.cc/PCX5Rs9D)

### 添加Redis缓存

#### 1.添加商户查询缓存

![image-20251218111733464](C:\Users\Minjie\AppData\Roaming\Typora\typora-user-images\image-20251218111733464.png)

**代码实现**

```java
@Service
public class ShopServiceImpl extends ServiceImpl<ShopMapper, Shop> implements IShopService {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public Result queryById(Long id) {
        String key = RedisConstants.CACHE_SHOP_KEY +id;
        //1.从redis查询商铺缓存
        String shopJson = stringRedisTemplate.opsForValue().get(key);
        //2.判断是否存在
        if(StrUtil.isNotBlank(shopJson)){
            //3.存在，直接返回
            Shop shop = JSONUtil.toBean(shopJson,Shop.class);
            return Result.ok(shop);
        }
        //4.不存在，根据id查询数据库
        Shop shop = getById(id);
        //5.数据库不存在，返回错误
        if(shop==null){
            return Result.fail("店铺不存在");
        }
        //6.存在，将查询结果写入redis
        stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(shop));

        //7.返回数据
        return Result.ok(shop);
    }
}
```

#### 2.添加商铺类型查询业务

[![image.png](https://i.postimg.cc/Dz0DqfCX/image.png)](https://postimg.cc/QBL0DDZ8)

**代码实现**

```java
@Service
public class ShopTypeServiceImpl extends ServiceImpl<ShopTypeMapper, ShopType> implements IShopTypeService {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public Result queryTypeList() {
        String typeKey= RedisConstants.CACHE_TYPE_KEY;
        //1.从redis中查询
        Long typeListSize = stringRedisTemplate.opsForList().size(typeKey);
        //2.redis存在数据
        if (typeListSize!=null&&typeListSize!=0){
            List<String> typeJsonList = stringRedisTemplate.opsForList().range(typeKey, 0, typeListSize-1);
            List<ShopType> typeList=new ArrayList<>();
            for (String typeJson : typeJsonList) {
                typeList.add(JSONUtil.toBean(typeJson,ShopType.class));
            }
            return Result.ok(typeList);
        }
        //3.redis不存在数据 查询数据库
        List<ShopType> typeList = query().orderByAsc("sort").list();
        //4.数据库中不存在相关数据
        if (typeList==null){
            //数据库不存在数据
            return Result.fail("发生错误");
        }
        //转换
        List<String> typeJsonList=new ArrayList<>();
        for (ShopType shopType : typeList) {
            typeJsonList.add(JSONUtil.toJsonStr(shopType));
        }
        //5.数据库存在数据 写入redis
        stringRedisTemplate.opsForList().rightPushAll(typeKey,typeJsonList);
        //6.返回数据
        return Result.ok(typeList);
    }
}
```

### 缓存更新策略

*当数据库的数据发生修改时，需要及时更新redis中的值*

#### 1.三种更新策略

[![image.png](https://i.postimg.cc/HWK846Bb/image.png)](https://postimg.cc/JtNhR5wh)

#### 2.主动更新策略

[![image.png](https://i.postimg.cc/vBwbZ6z3/image.png)](https://postimg.cc/WD8Q9zxk)

[![image.png](https://i.postimg.cc/SQrkmRzR/image.png)](https://postimg.cc/bdsKTzgf)

[![image.png](https://i.postimg.cc/FHf4bxC0/image.png)](https://postimg.cc/Mn8Nq1SG)

[![image.png](https://i.postimg.cc/XJW6b6jX/image.png)](https://postimg.cc/XZszfhy0)

#### 3.案例：实现商铺缓存和数据库的双写一致

**需求**

[![image.png](https://i.postimg.cc/MG1YCNPh/image.png)](https://postimg.cc/FfsccWQZ)

**代码实现**

```java
@Override
@Transactional
public Result update(Shop shop) {
    Long id = shop.getId();
    if(id==null){
        return Result.fail("店铺id不能为空");
    }
    //1.更新数据库
    updateById(shop);
    //2.删除缓存
    stringRedisTemplate.delete(RedisConstants.CACHE_SHOP_KEY+shop.getId());
    return Result.ok();
} 
```

### 缓存穿透

**问题详情**

[![image.png](https://i.postimg.cc/66zgy4WL/image.png)](https://postimg.cc/3W4nqRDd)

**缓存空对象***

[![image.png](https://i.postimg.cc/hPdLNN3H/image.png)](https://postimg.cc/4mXH92G5)

```java
public Shop queryWithPassThrough(Long id){
    String key = RedisConstants.CACHE_SHOP_KEY +id;
    //1.从redis查询商铺缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    //2.判断是否存在
    if(StrUtil.isNotBlank(shopJson)){
        //3.存在，直接返回
        Shop shop = JSONUtil.toBean(shopJson,Shop.class);
        return Result.ok(shop);
    }
    //判断是否为空值，如果是“”，就不用返回数据库的查询了，如果是null,就需要进行数据库的查询（因为数据库没有查到会默认传一个“”，省略了查数据库的这一步）
    if(shopJson != null){
        return Result.fail("店铺信息不存在！");
    }

    //4.不存在，根据id查询数据库
    Shop shop = getById(id);
    //5.数据库不存在，返回错误
    if(shop==null){
        //将空值写入redis
        stringRedisTemplate.opsForValue().set(key,"",RedisConstants.CACHE_NULL_TTL,TimeUnit.MINUTES);
        return Result.fail("店铺不存在");
    }
    //6.存在，将查询结果写入redis
    stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(shop),RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    //7.返回数据
    return Result.ok(shop);
}

```

### 缓存雪崩

![image-20251220150741785](C:\Users\Minjie\AppData\Roaming\Typora\typora-user-images\image-20251220150741785.png)

### 缓存击穿

[![image.png](https://i.postimg.cc/DzXBFVXw/image.png)](https://postimg.cc/CZwjHQ73)

[![image.png](https://i.postimg.cc/gc8Fs5St/image.png)](https://postimg.cc/pyW6X0tK)

[![image.png](https://i.postimg.cc/mgcSNxVz/image.png)](https://postimg.cc/PCjZTRgT)

#### 1.互斥锁

**案例：**

[![image.png](https://i.postimg.cc/HWBKK4gh/image.png)](https://postimg.cc/F1kDkS1y)

**解决办法**:`setnx`

[![image.png](https://i.postimg.cc/P5GMFPPQ/image.png)](https://postimg.cc/ZCc3WYPW)

**代码实现**

```java
@Override
public Result queryById(Long id) {
    //缓存穿透
    Shop shop = queryWithPassThrough(id);
    if(shop == null){
        return Result.fail("店铺查询失败");
    }
    //返回数据
    return Result.ok(shop);
}

//封装缓存穿透的代码——基于锁机制
public Shop queryWithPassThrough(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY +id;
    //1.从redis查询商铺缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    //2.判断是否存在，isNotBlank方法会剔除掉null,""的情况
    if(StrUtil.isNotBlank(shopJson)){
        //3.存在，直接返回
        return JSONUtil.toBean(shopJson,Shop.class);
    }
    //4.实现缓存重建
    //4.1 获取互斥锁
    String lockKey = RedisConstants.LOCK_SHOP_KEY+id;
    Shop shop = null;
    try {
        boolean isLock = tryLock(lockKey);
        //4.2 判断是否获取成功
        if(!isLock){
            //4.3 获取失败，休眠
            Thread.sleep(50);
            return queryWithPassThrough(id);  //递归处理
        }
        //4.4 成功，进行数据库的查询和缓存重建
        shop = getById(id);
        //模拟重建的超时
        Thread.sleep(200);

        //5.数据库不存在，返回错误
        if(shop==null){
            //将空值写入redis
            stringRedisTemplate.opsForValue().set(key,"",RedisConstants.CACHE_NULL_TTL,TimeUnit.MINUTES);
            return null;
        }
        //6.存在，将查询结果写入redis
        stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(shop),RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }finally {
        //7.释放互斥锁
        unlock(lockKey);
    }

    //8.返回数据
    return shop;
}

//获取锁
private boolean tryLock(String key){
    Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key,"1",RedisConstants.LOCK_SHOP_TTL,TimeUnit.SECONDS);
    //如果flag的值为null，自动进行拆箱将包装类转换为基本数据类型时会抛出NullPointerException
    return BooleanUtil.isTrue(flag);
}
//删除锁
private boolean unlock(String key){
    return stringRedisTemplate.delete(key);
}
```

#### 2.逻辑过期

**案例**

[![image.png](https://i.postimg.cc/HWzKRG5k/image.png)](https://postimg.cc/V5SD0VDx)

**代码实现**

```java
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

//封装缓存穿透的代码——基于逻辑过期
public Shop queryWithLogicalExpire(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY +id;
    //1.从redis查询商铺缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    //2.判断是否存在
    if(StrUtil.isBlank(shopJson)){
        //3.不存在，直接返回null
        return null;
    }
    //4.命中，需要把这个json对象反序列化
    RedisData redisDate = JSONUtil.toBean(shopJson, RedisData.class);
    Shop shop = JSONUtil.toBean((JSONObject) redisDate.getData();, Shop.class);
    LocalDateTime expireTime = redisDate.getExpireTime();

    //5，判断是否过期
    if (expireTime.isAfter(LocalDateTime.now())) {
        //5.1 未过期，直接返回店铺信息
        return shop;
    }
    //5.2 已过期，需要缓存重建
    //6.缓存重建
    //6.1 获取互斥锁
    String localKey = RedisConstants.LOCK_SHOP_KEY + id;
    boolean isLock = tryLock(localKey);
    //6.2 判断是否获取锁成功
    if(isLock){
        //6.3 成功，开启独立线程，实现缓存重建
        CACHE_REBUILD_EXECUTOR.submit(() -> {
            try {
                //重建缓存
                this.saveShopToRedis(id,20L);
            } catch (Exception e) {
                throw new RuntimeException(e);
            } finally {
                //释放锁
                unlock(localKey);
            }
        });
    }
    //6.4 返回过期的商铺信息
    return shop;
}

public void saveShopToRedis(Long id,Long expireSeconds){
    //1.查询店铺数据
    Shop shop = getById(id);
    Thread.sleep(200);
    //2.封装逻辑过期时间
    RedisData redisData = new RedisData();
    redisData.setData(shop);
    redisData.setExpireTime(LocalDateTime.now().plusSeconds(expireSeconds));
    //3.写入Redis
    stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY+id,JSONUtil.toJsonStr(redisData));
}
```

### 缓存封装工具

#### 1.方法类型

[![image.png](https://i.postimg.cc/90P9wy7M/image.png)](https://postimg.cc/T5w1ZLQM)

#### 2.代码封装

```java
@Slf4j
@Component
public class CacheClient {
    private final StringRedisTemplate stringRedisTemplate;

    public CacheClient(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    public void set(String key, Object value, Long time, TimeUnit unit){
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }

    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit){
        RedisData redisData = new RedisData();
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        redisData.setData(value);
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }

    public <R,ID> R queryWithPassThrough(
            String keyPrefix, ID id, Class<R> type, Function<ID,R> dbFallback, Long time, TimeUnit unit){
        String key = keyPrefix +id;
        //1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        //2.判断是否存在
        if(StrUtil.isNotBlank(json)){
            //3.存在，直接返回
            return JSONUtil.toBean(json,type);
        }
        //判断是否为空值，如果是“”，就不用返回数据库的查询了，如果是null,就需要进行数据库的查询（因为数据库没有查到会默认传一个“”，省略了查数据库的这一步）
        if(json != null){
            return null;
        }

        //4.不存在，根据id查询数据库
        R r =  dbFallback.apply(id);
        //5.数据库不存在，返回错误
        if(r==null){
            //将空值写入redis
            stringRedisTemplate.opsForValue().set(key,"",RedisConstants.CACHE_NULL_TTL,TimeUnit.MINUTES);
            return null;
        }
        //6.存在，将查询结果写入redis
        this.stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(r),time,unit);

        //7.返回数据
        return r;
    }

    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

    //封装缓存穿透的代码——基于逻辑过期
    public <R,ID> R queryWithLogicalExpire(
            String prefix, ID id, Class<R> type, Function<ID,R> dbFallback, Long time, TimeUnit unit) {
        String key = prefix +id;
        //1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        //2.判断是否存在
        if(StrUtil.isBlank(json)){
            //3.不存在，直接返回null
            return null;
        }
        //4.命中，需要把这个json对象反序列化
        RedisData redisDate = JSONUtil.toBean(json, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisDate.getData(), type);
        LocalDateTime expireTime = redisDate.getExpireTime();

        //5，判断是否过期
        if (expireTime.isAfter(LocalDateTime.now())) {
            //5.1 未过期，直接返回店铺信息
            return r;
        }
        //5.2 已过期，需要缓存重建
        //6.缓存重建
        //6.1 获取互斥锁
        String localKey = RedisConstants.LOCK_SHOP_KEY + id;
        boolean isLock = tryLock(localKey);
        //6.2 判断是否获取锁成功
        if(isLock){
            //6.3 成功，开启独立线程，实现缓存重建
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                try {
                    //查询数据库
                    R r1 = dbFallback.apply(id);
                    //写入redis
                    this.setWithLogicalExpire(key,r1,time,unit);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                } finally {
                    //释放锁
                    unlock(localKey);
                }
            });
        }
        //6.4 返回过期的商铺信息
        return r;
    }

    //获取锁
    private boolean tryLock(String key){
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key,"1",RedisConstants.LOCK_SHOP_TTL,TimeUnit.SECONDS);
        //如果flag的值为null，自动进行拆箱将包装类转换为基本数据类型时会抛出NullPointerException
        return BooleanUtil.isTrue(flag);
    }
    //删除锁
    private boolean unlock(String key){
        return stringRedisTemplate.delete(key);
    }
}
```

#### 3.实现样例

```java
//缓存穿透
        //Shop shop = cacheClient.
        //queryWithPassThrough(RedisConstants.CACHE_SHOP_KEY,id,Shop.class,this::getById, RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);

        //逻辑过期解决缓存击穿
        Shop shop = cacheClient.
                queryWithLogicalExpire(RedisConstants.CACHE_SHOP_KEY,id,Shop.class,this::getById,20L,TimeUnit.SECONDS);
```

## 3.优惠券秒杀

### 全局唯一ID

#### 1.问题来源

[![image.png](https://i.postimg.cc/MHnSNhCQ/image.png)](https://postimg.cc/Pvn98FrX)

#### 2.全局id生成器

[![image.png](https://i.postimg.cc/MG1Xrj76/image.png)](https://postimg.cc/Lq6HJ5Qw)

[![image.png](https://i.postimg.cc/gJkdqw4g/image.png)](https://postimg.cc/KKVdm86T)

[![image.png](https://i.postimg.cc/25BbnK9Y/image.png)](https://postimg.cc/4KsxgBT2)

#### 3.代码实现

```java
private static final long BEGIN_TIMESTAMP = 1735689600;
//序列号位数
private static final int COUNT_BITS = 32;

@Resource
private StringRedisTemplate stringRedisTemplate;

public long nextId(String keyPrefix){

    //1.生成时间戳,当前时间-初始时间
    LocalDateTime now = LocalDateTime.now();
    long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
    long timeStamp = nowSecond-BEGIN_TIMESTAMP;

    //2.生成序列号,为了防止序列号进行超标，所以引入了时间key
    String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
    Long count = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" + date);

    //3.借助位运算拼接并返回
    return timeStamp << COUNT_BITS | count;
}
```

#### 4.并发测试

```java
@Test
void testIdWorker() throws InterruptedException {

    // CountDownLatch：并发控制工具，这里初始计数为 300（对应 300 个并发任务）
    CountDownLatch latch = new CountDownLatch(300);

    // 定义一个并发任务 Runnable
    Runnable task = () -> {
        // 每个线程循环生成 100 个 ID
        for(int i=0;i<100;i++){
            // 调用 RedisIdWorker 生成分布式唯一 ID，"order" 是业务前缀
            long id = redisIdWorker.nextId("order");
            // 打印生成的 ID，方便观察是否有重复
            System.out.println("id = "+id);
        }
        //把 CountDownLatch 的计数器减 1
        latch.countDown();
    };

    // 记录开始时间，用于统计生成 ID 的耗时
    long begin = System.currentTimeMillis();

    // 向线程池提交 300 个并发任务
    for(int i=0;i<300;i++){
        executorService.execute(task);
    }
    // 主线程阻塞，等待所有任务执行完成
    latch.await();
    // 记录结束时间
    long end = System.currentTimeMillis();

    // 输出从提交任务到当前时刻的耗时
    System.out.println("time:"+(end-begin));
}
```

### 实现优惠券秒杀下单

#### 1.需求分析

[![image.png](https://i.postimg.cc/13b4RKmG/image.png)](https://postimg.cc/XrcnQdyq)

#### 2.添加优惠券(秒杀)

**代码实现：**

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
}
```

**post测试样例**：http://localhost:8081/voucher/seckill

```json
{
  "shopId": 1,
  "title": "100元代金券",
  "subTitle": "新用户专享",
  "rules": "仅限堂食使用，不可叠加其他优惠",
  "payValue": 7000,
  "actualValue": 10000,
  "type": 1,
  "stock": 100,
  "beginTime": "2025-01-01T10:00:00",
  "endTime": "2030-01-07T23:59:59"
}
```

#### 3.秒杀下单

**功能需求**

[![image.png](https://i.postimg.cc/W3N7SVBL/image.png)](https://postimg.cc/2qtv8sj2)

[![image.png](https://i.postimg.cc/c1VFypYF/image.png)](https://postimg.cc/237QnX0W)

**代码实现**

```java
@Resource
private ISeckillVoucherService seckillVoucherService;

@Resource
private RedisIdWorker redisIdWorker;

@Override
@Transactional
public Result seckillVoucher(Long voucherId) {
    //1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    //2.判断秒杀是否开始
    if(voucher.getBeginTime().isAfter(LocalDateTime.now())){
        //尚未开始
        return Result.fail("秒杀尚未开始");
    }
    //3.判断秒杀是否已经结束
    if(voucher.getEndTime().isBefore(LocalDateTime.now())){
        //已经结束
        return Result.fail("秒杀已经结束");
    }
    //4.判断库存是否充足
    if(voucher.getStock() < 1){
        //库存不足
        return Result.fail("库存不足！");
    }
    //5.扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock = stock - 1")
            .eq("voucher_id", voucherId).update();
    if(!success){
        return Result.fail("库存不足！");
    }
    //6.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    //6.1生成全局唯一的订单Id
    Long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    //6.2设置当前线程内的用户id
    voucherOrder.setUserId(UserHolder.getUser().getId());
    //6.3设置代金券id
    voucherOrder.setVoucherId(voucherId);
    //6.4保存到数据库
    save(voucherOrder);

    //7.返回订单
    return Result.ok(orderId);
}
```

### 超卖问题

#### 1.问题来源

[![image.png](https://i.postimg.cc/NMmLkZgt/image.png)](https://postimg.cc/CBMFFPBr)

#### 2.解决方案

[![image.png](https://i.postimg.cc/W3L1zS0C/image.png)](https://postimg.cc/gwsWBVSH)

[![image.png](https://i.postimg.cc/TwNLwnY5/image.png)](https://postimg.cc/N9TfC9XQ)

#### 3.乐观锁

**版本号法**

[![image.png](https://i.postimg.cc/CLFHdsNq/image.png)](https://postimg.cc/F1BJBcwr)

**CAS法**

[![image.png](https://i.postimg.cc/K8CM7Pnt/image.png)](https://postimg.cc/Sn7Rkz1K)

#### 4.代码实现

```java
//5.扣减库存
boolean success = seckillVoucherService.update()
        .setSql("stock = stock - 1")
        .eq("voucher_id", voucherId).gt("stock",0).update();
```

### 一人一单

#### 1.解决方案

[![image.png](https://i.postimg.cc/fLStVJtQ/image.png)](https://postimg.cc/njZc5VG3)

#### 2.代码实现

**`pom.xml`中添加依赖**

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
</dependency>
```

**添加启动类**

```java
@EnableAspectJAutoProxy(exposeProxy = true)
```

**悲观锁解决一人一单**

```java
@Override
public Result seckillVoucher(Long voucherId) {
    //1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    //2.判断秒杀是否开始
    if(voucher.getBeginTime().isAfter(LocalDateTime.now())){
        //尚未开始
        return Result.fail("秒杀尚未开始");
    }
    //3.判断秒杀是否已经结束
    if(voucher.getEndTime().isBefore(LocalDateTime.now())){
        //已经结束
        return Result.fail("秒杀已经结束");
    }
    //4.判断库存是否充足
    if(voucher.getStock() < 1){
        //库存不足
        return Result.fail("库存不足！");
    }
    Long userId = UserHolder.getUser().getId();
    synchronized (userId.toString().intern()) {
        //获取代理对象，避免没提交到数据库就释放锁的安全问题
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createVoucherOrder(voucherId);
    }
}

@Transactional
public Result createVoucherOrder(Long voucherId) {
    //5.一人一单
    Long userId = UserHolder.getUser().getId();

    //5.1查询订单
    Long count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    //5.2判断是否存在
    if(count > 0L){
        //用户已经购买过了
        return Result.fail("用户已经购买过一次！");
    }

    //6.扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock = stock - 1")
            .eq("voucher_id", voucherId).gt("stock",0).update();
    if(!success){
        return Result.fail("库存不足！");
    }

    //7.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    //7.1生成全局唯一的订单Id
    Long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    //7.2设置当前线程内的用户id
    voucherOrder.setUserId(UserHolder.getUser().getId());
    //7.3设置代金券id
    voucherOrder.setVoucherId(voucherId);
    //7.4保存到数据库
    save(voucherOrder);

    //8.返回订单
    return Result.ok(orderId);
}
```

### 分布式锁

#### 1.集群模式下的并发安全问题（TODO）

**添加集群**

[![image.png](https://i.postimg.cc/jSQSVkwH/image.png)](https://postimg.cc/XrqbdLgq)

**配置反向代理**

[![image.png](https://i.postimg.cc/5289XTmX/image.png)](https://postimg.cc/zyXYMd6N)

**重启nginx**` nginx.exe -s reload`

**问题来源**

[![image.png](https://i.postimg.cc/Gm15Nbyp/image.png)](https://postimg.cc/w702R8FK)

#### 2.分布式锁原理

[![image.png](https://i.postimg.cc/K8vn4VXQ/image.png)](https://postimg.cc/xchk461b)

[![image.png](https://i.postimg.cc/3xcDNxFJ/image.png)](https://postimg.cc/qN27Z0F9)

![image-20251223143400696](C:\Users\Minjie\AppData\Roaming\Typora\typora-user-images\image-20251223143400696.png)

#### 3.redis实现分布式锁

[![image.png](https://i.postimg.cc/GtwfCR5z/image.png)](https://postimg.cc/5YggBh1F)

[![image.png](https://i.postimg.cc/R0QydVLv/image.png)](https://postimg.cc/mckm24nn)

**代码实现**

```java 
public class SimpleRedisLock implements ILock{

    private final String name;
    private final StringRedisTemplate stringRedisTemplate;

    public SimpleRedisLock(String name,StringRedisTemplate stringRedisTemplate){
        this.name = name;
        this.stringRedisTemplate = stringRedisTemplate;
    }

    private static final String KEY_PREFIX = "lock:";

    @Override
    public boolean tryLock(Long timeoutSec) {
        //获取线程标识
        long threadId = Thread.currentThread().getId();
        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().
                setIfAbsent(KEY_PREFIX + name, threadId + "", timeoutSec, TimeUnit.SECONDS);
        //防止拆箱为NULL
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        //释放锁
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

**使用样例**

```java
//创建锁对象
SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
//获取锁
boolean isLock = lock.tryLock(1200L);
//判断锁是否获取成功
if(!isLock){
    //获取锁失败或者重试
    return Result.fail("一个人不允许重复下单");
}

try {
    //获取代理对象，避免没提交到数据库就释放锁的安全问题
    IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
    return proxy.createVoucherOrder(voucherId);
} catch (IllegalStateException e) {
    throw new RuntimeException(e);
} finally {
    lock.unlock();
}
```

#### 4.误删问题

**问题来源**：业务未完成超时释放锁 。导致线程2，3全都获取锁执行成功 

[![image.png](https://i.postimg.cc/596mtRzw/image.png)](https://postimg.cc/G9nD7zq2)

**解决办法：**判断获取锁的标识前后是否一致。

[![image.png](https://i.postimg.cc/fTy1DCzC/image.png)](https://postimg.cc/KRX05ng3)

**代码实现**

```java
private static final String KEY_PREFIX = "lock:";
private static final String ID_PREFIX = UUID.randomUUID().toString() + '-';

@Override
public boolean tryLock(Long timeoutSec) {
    //获取线程标识
    String threadId = ID_PREFIX+Thread.currentThread().getId();
    //获取锁
    Boolean success = stringRedisTemplate.opsForValue().
            setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
    //防止拆箱为NULL
    return Boolean.TRUE.equals(success);
}

@Override
public void unlock() {
    //获取线程标识
    String threadId = ID_PREFIX + Thread.currentThread().getId();
    //获取锁中的标识
    String id = stringRedisTemplate.opsForValue().get(KEY_PREFIX + name);
    if(threadId.equals(id)){
        //释放锁
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

#### 5.原子性问题

**问题来源**：*判断锁标识*和*释放锁*之间产生了阻塞问题，导致**锁超时自动释放**，导致线程2，3并发安全问题、导致于线程1，3的执行都成功

[![image.png](https://i.postimg.cc/h4MC5mBc/image.png)](https://postimg.cc/jCwQWL2F)

**Lua语言**

[![image.png](https://i.postimg.cc/QtR0p2KK/image.png)](https://postimg.cc/zbFCNPjq)

**Lua语言的执行**

[![image.png](https://i.postimg.cc/4NTTGM2N/image.png)](https://postimg.cc/G9zfJM1Z)

**Lua脚本:实现判断锁标识和释放锁的一致性**

```lua
-- 比较线程标识与锁中的线程标识是否一致

if(redis.call('get',KEYS[i]) == ARGV[i]) then
    -- 释放锁 del key
    return redis.call('del',KEYS[i])
end
return 0
```

**java调用这个lua脚本**

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;
//初始化脚本
static {
    UNLOCK_SCRIPT = new DefaultRedisScript<>();
    UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
    UNLOCK_SCRIPT.setResultType(Long.class);
}

@Override
public void unlock() {
    //调用lua脚本
    stringRedisTemplate.execute(
            UNLOCK_SCRIPT,
            Collections.singletonList(KEY_PREFIX+name),
            ID_PREFIX+Thread.currentThread().getId()
    );
}
```

#### 6.Redisson重构

**问题来源**

![](https://pic1.imgdb.cn/item/694b3effc3594c4bdf9cb26f.png)



### Redis秒杀优化

### Redis消息队列实现异步秒杀优化

## 4.达人探店

## 5.好友关注

## 6.附近商铺搜索

## 7.用户签到

## 8.UV统计




