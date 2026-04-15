# 机票推荐系统

基于 Spring Boot 3.4 的机票推荐系统后端服务，为用户提供航班搜索、比价、个性化推荐、价格趋势、低价提醒等功能。

## 项目概述

机票推荐系统旨在帮助用户快速找到合适的航班，支持按出发地、目的地、日期、价格等条件筛选，并结合用户偏好进行智能推荐。并且在后端做一小ai模型来算推荐

## 技术栈

- Java 21
- Spring Boot 3.4.1
- Spring Web、Security、AOP
- MyBatis + H2 / MySQL / PostgreSQL
- PageHelper（分页）
- Spring Data Redis（可选缓存）
- SpringDoc OpenAPI（Swagger）
- Flyway（数据库迁移）
- Spring Mail（邮件通知）

## 已实现功能
- [x] 航班搜索（出发地、目的地、日期、分页、排序、过滤）
- [x] 航班比价与多维度排序（价格、时长、综合）
- [x] 个性化推荐（热门、常搜航线、你可能喜欢）
- [x] 用户偏好记录（AOP 埋点）
- [x] 价格趋势查询
- [x] 低价提醒 + 邮件通知
- [x] 热门航线排行
- [x] API 文档（Swagger UI）
- [x] 健康检查（Actuator）

## API 接口总览

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/health` | GET | 健康检查 |
| `/api/auth/register` | POST | 用户注册 |
| `/api/auth/login` | POST | 用户登录 |
| `/api/flights` | GET | 航班列表 |
| `/api/flights/{id}` | GET | 航班详情 |
| `/api/flights/search` | GET | 高级搜索（分页、排序、过滤） |
| `/api/recommendations` | GET | 个性化推荐 |
| `/api/routes/popular` | GET | 热门航线排行 |
| `/api/price/trend` | GET | 价格趋势 |
| `/api/price/alert` | POST/GET | 低价提醒（创建/列表） |
| `/swagger-ui.html` | GET | API 文档 |
| `/actuator/health` | GET | 应用健康 |

## 快速开始

### 启动应用

```bash
mvn spring-boot:run
```

或指定环境：

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

### 验证运行

访问 http://localhost:8080/api/health 应返回：

```json
{"status":"UP","application":"springboot-app"}
```

### API 文档

启动后访问 http://localhost:8080/swagger-ui.html 可在线调试所有接口。

### 测试账号

- 用户名：`testuser`
- 密码：`123456`

### 示例：航班搜索

```
GET /api/flights/search?departure=PEK&arrival=PVG&departureDate=2025-03-18&page=0&size=10&sortBy=PRICE_ASC
```

### 打包

```bash
mvn clean package
java -jar target/springboot-app-1.0.0-SNAPSHOT.jar
```

## 项目结构

src/main/java/com/example/app/
├── SpringbootAppApplication.java

├── aspect/
│   └── UserBehaviorAspect.java

├── common/
│   ├── GlobalExceptionHandler.java
│   └── Result.java

├── config/
│   ├── AiConfig.java                  # ⭐ AI配置（API Key / Provider）
│   ├── CacheConfig.java
│   ├── OpenApiConfig.java
│   ├── RedisCacheConfig.java
│   ├── RedisConfig.java
│   ├── SecurityConfig.java
│   └── WebConfig.java

├── controller/
│   ├── AiController.java              # ⭐ AI接口（满意度反馈 / AI分析）
│   ├── AuthController.java
│   ├── FlightController.java
│   ├── HealthController.java
│   ├── PriceController.java
│   ├── RecommendationController.java
│   └── RouteController.java

├── dto/
│   ├── ai/                            # ⭐ AI相关DTO（单独分包，面试加分）
│   │   ├── AiFeedbackRequest.java
│   │   ├── AiFeedbackResponse.java
│   │   ├── AiAnalysisResult.java
│   │   └── AiRecommendRequest.java    # （可扩展）
│   │
│   ├── FlightSearchParams.java
│   ├── FlightSearchRequest.java
│   ├── FlightSearchResponse.java
│   ├── FlightVO.java
│   ├── LoginRequest.java
│   ├── PageResult.java
│   ├── PriceAlertRequest.java
│   └── RegisterRequest.java

├── entity/
│   ├── Airline.java
│   ├── Airport.java
│   ├── Booking.java
│   ├── Flight.java
│   ├── FlightPrice.java
│   ├── PriceAlert.java
│   ├── Route.java
│   ├── User.java
│   ├── UserFlightHistory.java
│   └── UserFeedback.java             # ⭐ 新增：用户反馈（满意/不满意）

├── mapper/
│   ├── AirlineMapper.java
│   ├── AirportMapper.java
│   ├── BookingMapper.java
│   ├── FlightMapper.java
│   ├── FlightPriceMapper.java
│   ├── PriceAlertMapper.java
│   ├── RouteMapper.java
│   ├── UserFlightHistoryMapper.java
│   ├── UserMapper.java
│   └── UserFeedbackMapper.java       # ⭐ 新增

├── scheduler/
│   ├── PriceScheduler.java
│   └── AiOptimizationScheduler.java  # ⭐ 可选：定期优化推荐策略

├── security/
│   └── AuthUser.java

├── service/
│   ├── ai/                           # ⭐ AI核心模块（重点）
│   │   ├── AiService.java
│   │   ├── AiServiceImpl.java
│   │   ├── AiClient.java             # 调用AI（HTTP/OpenAI）
│   │   ├── PromptBuilder.java        # Prompt构建（核心）
│   │   └── AiResponseParser.java     # 解析AI返回
│   │
│   ├── AirportService.java
│   ├── AuthService.java
│   ├── FlightService.java
│   ├── NotificationService.java
│   ├── PriceAlertService.java
│   ├── PriceTrendService.java
│   ├── RecommendationService.java    # ⭐ 会调用AI做rerank
│   ├── RouteService.java
│   ├── UserBehaviorService.java
│   ├── UserDetailsServiceImpl.java
│   ├── UserService.java
│   └── UserFeedbackService.java      # ⭐ 处理用户反馈

└── ...

## 配置说明

- **开发环境**：默认排除 Redis，无需本地安装
- **Redis 缓存**：激活 `redis` profile 可启用 Redis 缓存
- **邮件通知**：需配置 `spring.mail.*`，否则低价提醒仅记录不发送

## 后续扩展建议

1. 对接真实航司或第三方航班数据 API
2. 接入 MySQL/PostgreSQL 生产数据库
3. 增强推荐算法（协同过滤、机器学习）
4. 前端 Web 或移动端

3. 增强推荐算法（协同过滤、机器学习）
4. 前端 Web 或移动端
