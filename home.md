# 基础概念

- 同比

  与历史“同时期”比较，例如2011年3月份与2010年3月份相比，叫“同比”，“同比期”又称“同期”。

  同比 = (本期 - 同比期)/同比期 *100%

- 环比

  与“上一个”统计周期比较，例如2011年4月份与2011年3月份相比较，称为“环比”。

  同比 = (本期 - 环比期)/环比期 *100%

- 全量

  对于大盘：统计周期内，所有的数据，包含**已解约门店**。

  对于各级经理：统计周期内，所有的数据。鉴于已解约门店无法分配经理，因此本期门店数>对比期门店数。

- 存量

  统计周期内，门店营业额均>0的门店。**数据部统计存量，限定“门店状态”为“营业中”。**

- 当前

  当前“营业状态”为“营业中”的门店。

- 时间进度

  统计日期/当月天数 *100%。



# u8 cloud 报货信息

u8c常用数据源表为**销售订单**与**销售订单明细**。



## 销售订单

- 选项位置：“供应链”→“销售管理”→“销售订单”→“销售订单”。

  *已添加至“我的工作”快捷方式。*

- 查询：点击“查询”按钮，打开“查询条件输入”框，选择自定义条件框。订单类型通常选择“全部”。

- 数据导出：`ctrl+a`，`ctrl+v` 。

  

## 销售订单明细

- 选项位置：“供应链”→“销售管理”→“销售订单查询”→“销售订单明细”。

  *已添加至“我的工作”快捷方式。*

- 查询：点击“查询”按钮，查询单品报货最好使用“存货编码”更加准确。

- 数据导出：`ctrl+a`，`ctrl+v` ，或“预览”→“输出excel”。

  

## 库存

- 选项位置：“供应链”→“库存管理”→“查询统计”→“收发存汇总表”。

  *已添加至“我的工作”快捷方式。*

- 查询：点击“查询”按钮查询。

- 数据导出：Excel输出，直接打印输出。

  

## 常用存货编码

| 存货编码  | 存货名称               | 报货日期  | 备注   |
| --------- | ---------------------- | --------- | ------ |
| 040000045 | 柠檬                   |           |        |
| 040000006 | 橙子                   |           |        |
| 040001022 | 调味糖浆               |           |        |
| 120001482 | 糯香观音组合套装       | 2024/2/20 | 420杯  |
| 120001511 | 碧玉桃花组合套装       | 2024/3/7  | 420杯  |
| 120001535 | 清风茉白升级宣传物料包 | 2024/3/13 |        |
| 040001536 | 2024版清风茉白-A套餐   | 2024/3/15 | (新)   |
| 040001537 | 2024版清风茉白-B套餐   |           | （旧） |
| 120001549 | 牛油果宣传物料包       | 2024/3/23 |        |
| 040000807 | 冷冻牛油果泥           | 2024/3/23 |        |
| 060000019 | PLA粗吸管              |           |        |
| 060000020 | PLA细吸管              |           |        |
| 060000889 | PLA粗吸管（黑）        |           |        |
| 040001410 | 龙麟香组合套装         | 2024/1/6  |        |
| 040000983 | 速冻桑葚浆             |           |        |
| 120001554 | 超仙黑桑葚宣传物料包   | 2024/4/9  | 4800套 |
| 040001555 | 2024版桑葚包材套装     | 2024/4/9  | 4500套 |
| 050001493 | 2024版水桶杯           | 2024/3/25 |        |
| 050001495 | 2024版水桶杯配件       | 2024/3/25 |        |
| 060001508 | 2024版水桶杯盖         | 2024/3/25 |        |
| 120001602 | 520果茶宣传物料包      | 2024/5/9  |        |
| 40001603  | 520果茶套装            |           |        |


# 中台的使用

中台的地址为：https://data.tianlala.com/ 

**注：**

1. 中台数据**每天凌晨更新，非即时数据**。即时数据应到相应平台提取。

2. 中台单次最多导出30万条数据。

3. 中台采用Mysql数据库，因此使用mysql语句。

   

## 数据库查询

目前数据部具有查询权限的表为`ads_dbs_report_food_di`、`ads_dbs_trade_shop_di`、`ads_dbs_trade_food_di`。

```sql
-- 查看数据库信息
SELECT DATABASE();
```

```sql
-- 查看数据库版本
SELECT VERSION();
```

返回`tll_bi_dw	`，所有表都是在`tll_bi_dw`中的，数据库版本`5.7.99	`。



### 表`ads_dbs_report_food_di`

主要用于查询门店报货信息。

**注：**

- 中台数据非即时数据，数据截至前一天。
- 收银数据，如流水金额（`total_amount`）为当日流水，`sum(total_amount)`写法是没有什么意义，是错误的，应当使用`ads_dbs_trade_shop_di`表计算一段时间的流水。
- 此表未还原二八分账，报货金额需除以0.8。

例：

```mysql
SELECT
    LEFT(business_date, 6) AS 月份,
    stat_shop_name AS 门店名称,
    SUM(report_amount) / 0.8 AS 报货金额
FROM
    ads_dbs_report_food_di
WHERE
    stat_shop_id = 'TLL03855'
GROUP BY
    门店名称,
    月份
```

表`ads_dbs_report_food_di`包含字段如下：

| 字段                    | 类型    | 解释                    |
| ----------------------- | ------- | ----------------------- |
| business_date           | VARCHAR | 日期                    |
| stat_shop_id            | VARCHAR | 门店编号                |
| stat_shop_name          | VARCHAR | 门店名称                |
| license_person          | VARCHAR | 法人名称                |
| prov_id                 | VARCHAR | 省份编码                |
| prov_name               | VARCHAR | 省份名称                |
| city_id                 | VARCHAR | 城市编码                |
| city_name               | VARCHAR | 城市名称                |
| city_level              | VARCHAR | 城市等级                |
| district_id             | VARCHAR | 区县编码                |
| district_name           | VARCHAR | 区县名称                |
| busi_area_type          | VARCHAR | 商圈                    |
| region_manager_name     | VARCHAR | 大区经理                |
| prov_manager_name       | VARCHAR | 省经理                  |
| district_manager_name   | VARCHAR | 区域经理                |
| total_amount            | DECIMAL | 流水金额                |
| pay_amount              | DECIMAL | 实收金额                |
| report_amount           | DECIMAL | 报货金额                |
| orange_report_cnt       | BIGINT  | 橙子报货数量（15kg/件） |
| is_orange_report        | INT     | 橙子是否报货            |
| lemon_report_cnt        | BIGINT  | 柠檬报货数量（15kg/件） |
| is_lemon_report         | INT     | 柠檬是否报货            |
| report_amount_last_day  | DECIMAL | 上一天报货金额          |
| report_amount_last_week | DECIMAL | 上周同期报货金额        |
| report_amount_last_year | DECIMAL | 去年同期报货金额        |
| load_time               | VARCHAR | 数据更新时间            |

### 表`ads_dbs_trade_shop_di`

用于查询各店铺日销售

**注：**

- 数据逻辑

  > POS = AI点餐系统 + POS点餐 + 快应用-VIVO
  >
  > 小程序 = 三方对接 + 微信小程序 +支付宝小程序

- 中台数据非即时数据，数据截至前一天。
- 受美团管家api限制，中台数据准确性通常在±0.1%左右，偶尔差距比较大。制作绩效等场合不应使用此表数据。
- 为减少api所带来的影响，中台会**回刷向前10日数据**，今天查询和昨天查询会有些许不同是正常情况。

例：

```mysql
SELECT
    LEFT(business_date, 6) AS 月份,
    stat_shop_name AS 门店名称,
    platform AS 平台,
    SUM(total_amount) AS 流水金额
FROM
    ads_dbs_trade_shop_di
WHERE
    stat_shop_id = 'TLL03855'
GROUP BY
    门店名称,
    平台,
    月份
```

表`ads_dbs_trade_shop_di`包含字段如下：

| 字段                          | 类型    | 解释                                                  |
| ----------------------------- | ------- | ----------------------------------------------------- |
| business_date                 | BIGINT  | 日期                                                  |
| oper_center_name              | VARCHAR | 营运中心                                              |
| prov_id                       | VARCHAR | 省份编码                                              |
| prov_name                     | VARCHAR | 省份名称                                              |
| city_id                       | VARCHAR | 城市编码                                              |
| city_name                     | VARCHAR | 城市名称                                              |
| city_level                    | VARCHAR | 城市等级                                              |
| district_id                   | VARCHAR | 区县编码                                              |
| district_name                 | VARCHAR | 区县名称                                              |
| busi_area_type                | VARCHAR | 商圈                                                  |
| bussiness_type                | VARCHAR | 业务类型                                              |
| anal_type                     | VARCHAR | 分析类型:到家、到店                                   |
| platform                      | VARCHAR | 平台:到家：美团、饿了么、其它 到店：POS、小程序、其它 |
| stat_shop_id                  | VARCHAR | 门店统计id                                            |
| stat_shop_name                | VARCHAR | 门店统计名称                                          |
| total_amount                  | DECIMAL | 流水金额                                              |
| total_amount_last_day         | DECIMAL | 上一天流水金额                                        |
| total_amount_last_week        | DECIMAL | 上一周流水金额                                        |
| total_amount_last_year        | DECIMAL | 去年同期流水金额                                      |
| pay_amount                    | DECIMAL | 实收金额                                              |
| pay_amount_last_day           | DECIMAL | 上一天实收金额                                        |
| pay_amount_last_week          | DECIMAL | 上一周实收金额                                        |
| pay_amount_last_year          | DECIMAL | 去年同期实收金额                                      |
| order_count                   | BIGINT  | 订单量                                                |
| order_count_last_day          | BIGINT  | 上一天订单量                                          |
| order_count_last_week         | BIGINT  | 上一周订单量                                          |
| order_count_last_year         | BIGINT  | 去年同期订单量                                        |
| is_trd_shop                   | BIGINT  | 是否交易门店                                          |
| is_trd_shop_last_day          | BIGINT  | 上一天是否交易门店                                    |
| is_trd_shop_last_week         | BIGINT  | 上一周是否交易门店                                    |
| is_trd_shop_last_year         | BIGINT  | 去年同期是否交易门店                                  |
| discount_amount               | DECIMAL | 优惠金额                                              |
| discount_amount_last_day      | DECIMAL | 上一天优惠金额                                        |
| discount_amount_last_week     | DECIMAL | 上一周优惠金额                                        |
| discount_amount_last_year     | DECIMAL | 去年同期优惠金额                                      |
| dp_item_count                 | BIGINT  | 商品销量                                              |
| dp_item_count_last_day        | BIGINT  | 上一天商品销量                                        |
| dp_item_count_last_week       | BIGINT  | 上一周商品销量                                        |
| dp_item_count_last_year       | BIGINT  | 去年同期商品销量                                      |
| dp_total_amount               | DECIMAL | 商品流水金额                                          |
| dp_total_amount_last_day      | DECIMAL | 上一天商品流水金额                                    |
| dp_total_amount_last_week     | DECIMAL | 上一周商品流水金额                                    |
| dp_total_amount_last_year     | DECIMAL | 去年同期商品流水金额                                  |
| return_order_count            | BIGINT  | 退款订单量                                            |
| return_order_count_last_day   | BIGINT  | 上一天退款订单量                                      |
| return_order_count_last_week  | BIGINT  | 上一周退款订单量                                      |
| return_order_count_last_year  | BIGINT  | 去年同期退款订单量                                    |
| return_total_amount           | DECIMAL | 退款金额                                              |
| return_total_amount_last_day  | DECIMAL | 上一天退款金额                                        |
| return_total_amount_last_week | DECIMAL | 上一周退款金额                                        |
| return_total_amount_last_year | DECIMAL | 去年同期退款金额                                      |
| load_time                     | VARCHAR | 数据更新时间                                          |
| is_dj_sold_shop               | BIGINT  | 是否有外卖业绩门店                                    |
| is_dj_sold_shop_last_day      | BIGINT  | 上一天是否有外卖业绩门店                              |
| is_dj_sold_shop_last_week     | BIGINT  | 上一周是否有外卖业绩门店                              |
| is_dj_sold_shop_last_year     | BIGINT  | 去年同期是否有外卖业绩门店                            |
| is_wechat_community           | VARCHAR | 是否微信社群用户                                      |
| is_sign                       | VARCHAR | 是否签约                                              |
| franchisee_type               | VARCHAR | 加盟商类型：直营、加盟                                |
| is_doubt_shop                 | BIGINT  | 是否存疑门店                                          |
| up_1_order_count              | BIGINT  | 2杯及以上订单量                                       |
| up_1_order_count_last_day     | BIGINT  | 上一天2杯及以上订单量                                 |
| up_1_order_count_last_week    | BIGINT  | 上周同期2杯及以上订单量                               |
| up_1_order_count_last_year    | BIGINT  | 去年同期2杯及以上订单量                               |
| time_name                     | VARCHAR | 时段                                                  |
| channel_name                  | VARCHAR | 渠道                                                  |
| report_amount                 | DECIMAL | 报货金额                                              |
| report_amount_last_day        | DECIMAL | 上一天报货金额                                        |
| report_amount_last_week       | DECIMAL | 上周同期报货金额                                      |
| report_amount_last_year       | DECIMAL | 去年同期报货金额                                      |

### 表`ads_dbs_trade_food_di`

主要用于查询单店单日单品销售情况

- 中台数据非即时数据，数据截至前一天。
- 受美团管家api限制，中台数据准确性通常在±0.1%左右，偶尔差距比较大。制作绩效等场合不应使用此表数据。
- 为减少api所带来的影响，中台会**回刷向前10日数据**，今天查询和昨天查询会有些许不同是正常情况。
- 因套餐拆分映射可能更新不及时，查询菜品时应多使用通配符，如`like '%清风茉白%'`。

例：

```mysql
SELECT
    LEFT(business_date, 6) AS 月份,
    stat_shop_name AS 门店名称,
    platform AS 平台,
    SUM(dp_item_count) AS 销量
FROM
    ads_dbs_trade_food_di
WHERE
    item_name LIKE '%碧玉桃花%'
GROUP BY
    月份,
    门店名称,
    平台;
```

表`ads_dbs_trade_shop_di`包含字段如下：

| 字段                             | 类型    | 解释                                                   |
| -------------------------------- | ------- | ------------------------------------------------------ |
| business_date                    | BIGINT  | 日期                                                   |
| oper_center_name                 | VARCHAR | 营运中心                                               |
| prov_id                          | VARCHAR | 省份编码                                               |
| prov_name                        | VARCHAR | 省份名称                                               |
| city_id                          | VARCHAR | 城市编码                                               |
| city_name                        | VARCHAR | 城市名称                                               |
| city_level                       | VARCHAR | 城市等级                                               |
| district_id                      | VARCHAR | 区县编码                                               |
| district_name                    | VARCHAR | 区县名称                                               |
| busi_area_type                   | VARCHAR | 商圈                                                   |
| bussiness_type                   | VARCHAR | 业务类型                                               |
| channel_name                     | VARCHAR | 订单渠道                                               |
| anal_type                        | VARCHAR | 分析类型(到家、到店)                                   |
| platform                         | VARCHAR | 平台(到家：美团、饿了么、其它 到店：POS、小程序、其它) |
| cust_type                        | VARCHAR | 用户类型                                               |
| time_name                        | VARCHAR | 时段                                                   |
| food_category_name               | VARCHAR | 商品分类                                               |
| food_oneid                       | VARCHAR | 商品oneid                                              |
| item_name                        | VARCHAR | 商品名称                                               |
| new_flag                         | VARCHAR | 是否新品                                               |
| cup_type                         | VARCHAR | 杯型                                                   |
| ice_type                         | VARCHAR | 温度                                                   |
| sugar_type                       | VARCHAR | 甜度                                                   |
| dp_total_amount                  | DECIMAL | 商品流水金额                                           |
| dp_total_amount_last_day         | DECIMAL | 上一天商品流水金额                                     |
| dp_total_amount_last_week        | DECIMAL | 上一周商品流水金额                                     |
| dp_total_amount_last_year        | DECIMAL | 去年同期商品流水金额                                   |
| dp_item_count                    | BIGINT  | 商品销量                                               |
| dp_item_count_last_day           | BIGINT  | 上一天商品销量                                         |
| dp_item_count_last_week          | BIGINT  | 上一周商品销量                                         |
| dp_item_count_last_year          | BIGINT  | 去年同期商品销量                                       |
| dp_pay_amount                    | DECIMAL | 商品实收金额                                           |
| dp_pay_amount_last_day           | DECIMAL | 上一天商品实收金额                                     |
| dp_pay_amount_last_week          | DECIMAL | 上一周商品实收金额                                     |
| dp_pay_amount_last_year          | DECIMAL | 去年同期商品实收金额                                   |
| dp_discount_amount               | DECIMAL | 商品优惠金额                                           |
| dp_discount_amount_last_day      | DECIMAL | 上一天商品优惠金额                                     |
| dp_discount_amount_last_week     | DECIMAL | 上一周商品优惠金额                                     |
| dp_discount_amount_last_year     | DECIMAL | 去年同期商品优惠金额                                   |
| dp_return_item_count             | BIGINT  | 商品退货量                                             |
| dp_return_item_count_last_day    | BIGINT  | 上一天商品退货量                                       |
| dp_return_item_count_last_week   | BIGINT  | 上一周商品退货量                                       |
| dp_return_item_count_last_year   | BIGINT  | 去年同期商品退货量                                     |
| dp_return_total_amount           | DECIMAL | 商品退款金额                                           |
| dp_return_total_amount_last_day  | DECIMAL | 上一天商品退款金额                                     |
| dp_return_total_amount_last_week | DECIMAL | 上一周商品退款金额                                     |
| dp_return_total_amount_last_year | DECIMAL | 去年同期商品退款金额                                   |
| xl_total_amount                  | DECIMAL | 小料流水金额                                           |
| xl_total_amount_last_day         | DECIMAL | 上一天小料流水金额                                     |
| xl_total_amount_last_week        | DECIMAL | 上一周小料流水金额                                     |
| xl_total_amount_last_year        | DECIMAL | 去年同期小料流水金额                                   |
| xl_item_count                    | BIGINT  | 小料销量                                               |
| xl_item_count_last_day           | BIGINT  | 上一天小料销量                                         |
| xl_item_count_last_week          | BIGINT  | 上一周小料销量                                         |
| xl_item_count_last_year          | BIGINT  | 去年同期小料销量                                       |
| xl_pay_amount                    | DECIMAL | 小料实收金额                                           |
| xl_pay_amount_last_day           | DECIMAL | 上一天小料实收金额                                     |
| xl_pay_amount_last_week          | DECIMAL | 上一周小料实收金额                                     |
| xl_pay_amount_last_year          | DECIMAL | 去年同期小料实收金额                                   |
| tc_total_amount                  | DECIMAL | 套餐流水金额                                           |
| tc_total_amount_last_day         | DECIMAL | 上一天套餐流水金额                                     |
| tc_total_amount_last_week        | DECIMAL | 上一周套餐流水金额                                     |
| tc_total_amount_last_year        | DECIMAL | 去年同期套餐流水金额                                   |
| tc_item_count                    | BIGINT  | 套餐销量                                               |
| tc_item_count_last_day           | BIGINT  | 上一天套餐销量                                         |
| tc_item_count_last_week          | BIGINT  | 上一周套餐销量                                         |
| tc_item_count_last_year          | BIGINT  | 去年同期套餐销量                                       |
| tc_pay_amount                    | DECIMAL | 套餐实收金额                                           |
| tc_pay_amount_last_day           | DECIMAL | 上一天套餐实收金额                                     |
| tc_pay_amount_last_week          | DECIMAL | 上一周套餐实收金额                                     |
| tc_pay_amount_last_year          | DECIMAL | 去年同期套餐实收金额                                   |
| tc_discount_amount               | DECIMAL | 套餐优惠金额                                           |
| tc_discount_amount_last_day      | DECIMAL | 上一天套餐优惠金额                                     |
| tc_discount_amount_last_week     | DECIMAL | 上一周套餐优惠金额                                     |
| tc_discount_amount_last_year     | DECIMAL | 去年同期套餐优惠金额                                   |
| zb_total_amount                  | DECIMAL | 周边流水金额                                           |
| zb_total_amount_last_day         | DECIMAL | 上一天周边流水金额                                     |
| zb_total_amount_last_week        | DECIMAL | 上一周周边流水金额                                     |
| zb_total_amount_last_year        | DECIMAL | 去年同期周边流水金额                                   |
| zb_item_count                    | BIGINT  | 周边销量                                               |
| zb_item_count_last_day           | BIGINT  | 上一天周边销量                                         |
| zb_item_count_last_week          | BIGINT  | 上一周周边销量                                         |
| zb_item_count_last_year          | BIGINT  | 去年同期周边销量                                       |
| zb_pay_amount                    | DECIMAL | 周边实收金额                                           |
| zb_pay_amount_last_day           | DECIMAL | 上一天周边实收金额                                     |
| zb_pay_amount_last_week          | DECIMAL | 上一周周边实收金额                                     |
| zb_pay_amount_last_year          | DECIMAL | 去年同期周边实收金额                                   |
| load_time                        | VARCHAR | 数据更新时间                                           |
| item_type                        | VARCHAR | 商品类型（单品、套餐、小料、周边、其它）               |
| item_status                      | VARCHAR | 饮品状态(在线饮品、不在线饮品)                         |
| up_type                          | VARCHAR | 升级类型                                               |
| food_series                      | VARCHAR | 商品系列                                               |
| release_date                     | VARCHAR | 上新日期                                               |
| dp_order_count                   | BIGINT  | 商品订单量                                             |
| xl_order_count                   | BIGINT  | 小料订单量                                             |
| item_name_list                   | VARCHAR | 套餐下商品/小料明细                                    |
| other_total_amount               | DECIMAL | 其它饮品流水金额                                       |
| other_total_amount_last_day      | DECIMAL | 昨日其它饮品流水金额                                   |
| other_total_amount_last_week     | DECIMAL | 上周同期其它饮品流水金额                               |
| other_total_amount_last_year     | DECIMAL | 去年同期其它饮品流水金额                               |
| stat_shop_id                     | VARCHAR | 门店统计id                                             |
| stat_shop_name                   | VARCHAR | 门店统计名称                                           |
| send_whse                        | VARCHAR | 仓库                                                   |

## 需求实例

### 拉取单店各月营业额

拉取`TLL02973`各月的流水金额、实收金额。

```mysql
-- 修改门店编号即可
SELECT
    LEFT(business_date, 6) AS 月份,
    stat_shop_id AS 门店编号,
    SUM(total_amount) AS 流水金额,
    SUM(pay_amount) AS 实收金额
FROM
    ads_dbs_trade_shop_di
WHERE
    stat_shop_id = 'TLL02973'
GROUP BY
    门店编号,
    月份
```

运行结果

| 月份   | 门店名称             | 流水金额    | 实收金额    |
| ------ | -------------------- | ----------- | ----------- |
| 202301 | 正大鞋城正门东侧门市 | 58700.5     | 54178.6625  |
| 202302 | 正大鞋城正门东侧门市 | 48799.175   | 46594.525   |
| 202303 | 正大鞋城正门东侧门市 | 60516.2     | 55131.2375  |
| 202304 | 正大鞋城正门东侧门市 | 68786.225   | 62600.8625  |
| 202305 | 正大鞋城正门东侧门市 | 116276.0625 | 104340      |
| 202306 | 正大鞋城正门东侧门市 | 128220.025  | 117495.35   |
| 202307 | 正大鞋城正门东侧门市 | 110643.75   | 100312.025  |
| 202308 | 正大鞋城正门东侧门市 | 117987.0375 | 105989.3125 |
| 202309 | 正大鞋城正门东侧门市 | 84897.5     | 75668.6     |
| 202310 | 正大鞋城正门东侧门市 | 75673       | 65168.825   |
| 202311 | 正大鞋城正门东侧门市 | 48601.125   | 42019.2     |
| 202312 | 正大鞋城正门东侧门市 | 54445.75    | 45092.3625  |
| 202401 | 正大鞋城正门东侧门市 | 68583.375   | 55988.4     |
| 202402 | 正大鞋城正门东侧门市 | 56861       | 50365.15    |
| 202403 | 正大鞋城正门东侧门市 | 59744.75    | 50256.5125  |
| 202404 | 正大鞋城正门东侧门市 | 36446.125   | 31811.525   |

### 拉取单店时间段内的营业额、报货金额

```mysql
-- 需修改门店编号、开始日期、结束日期
WITH variables AS (
    SELECT 'TLL06486' AS shop_id, 20240101 AS start_date, 20240426 AS end_date
),
combined AS (
    SELECT
        stat_shop_id,
        report_amount,
        NULL AS total_amount,
        'ads_dbs_report_food_di' AS source
    FROM
        ads_dbs_report_food_di
    JOIN
        variables
    ON
        ads_dbs_report_food_di.stat_shop_id = variables.shop_id
        AND ads_dbs_report_food_di.business_date BETWEEN variables.start_date AND variables.end_date
    UNION ALL
    SELECT
        stat_shop_id,
        NULL,
        total_amount,
        'ads_dbs_trade_shop_di' AS source
    FROM
        ads_dbs_trade_shop_di
    JOIN
        variables
    ON
        ads_dbs_trade_shop_di.stat_shop_id = variables.shop_id
        AND ads_dbs_trade_shop_di.business_date BETWEEN variables.start_date AND variables.end_date
)
SELECT
    stat_shop_id AS 门店编号,
    SUM(CASE WHEN source = 'ads_dbs_report_food_di' THEN report_amount / 0.8 ELSE 0 END) AS 报货金额,
    SUM(CASE WHEN source = 'ads_dbs_trade_shop_di' THEN total_amount ELSE 0 END) AS 流水金额
FROM
    combined
GROUP BY
    门店编号;

```

运行结果

| 门店编号 | 报货金额 | 流水金额  |
| -------- | -------- | --------- |
| TLL06486 | 433982.7 | 451846.52 |

### 拉取各店时间段内的营业额、报货金额

```mysql
WITH combined AS (
    SELECT
        stat_shop_id,
        report_amount,
        NULL AS total_amount,
        'ads_dbs_report_food_di' AS source
    FROM
        ads_dbs_report_food_di
    WHERE
        business_date BETWEEN 20240101 AND 20240426
    UNION ALL
    SELECT
        stat_shop_id,
        NULL,
        total_amount,
        'ads_dbs_trade_shop_di' AS source
    FROM
        ads_dbs_trade_shop_di
    WHERE
        business_date BETWEEN 20240101 AND 20240426
)
SELECT
    stat_shop_id AS 门店编号,
    SUM(CASE WHEN source = 'ads_dbs_report_food_di' THEN report_amount / 0.8 ELSE 0 END) AS 报货金额,
    SUM(CASE WHEN source = 'ads_dbs_trade_shop_di' THEN total_amount ELSE 0 END) AS 流水金额
FROM
    combined
GROUP BY
    门店编号;

```

运行结果

| 门店编号 | 报货金额 | 流水金额  |
| -------- | -------- | --------- |
| TLL06295 | 44574    | 229111.2  |
| TLL05978 | 41780    | 59729.9   |
| TLL05518 | 37642    | 52091     |
| TLL05038 | 34422    | 74115     |
| TLL04418 | 244644   | 497057.99 |
| TLL03769 | 118506   | 197721    |
| TLL02558 | 86306.5  | 160705.3  |
| TLL01708 | 315663   | 606582.31 |
| TLL00130 | 188402   | 297594.56 |
| TLL07094 | 88487    | 163898.9  |



### 拉取各店最新销售日期

```mysql
-- 直接运行即可
SELECT
  stat_shop_id as 门店编码,
  MAX(business_date) as 上次收银日期
FROM
  ads_dbs_trade_shop_di
WHERE total_amount >0
GROUP BY
  stat_shop_id;
```

运行结果

| 门店编码 | 上次收银日期 |
| -------- | ------------ |
| TLL02135 | 20240423     |
| TLL03533 | 20240426     |
| TLL04785 | 20240426     |
| TLL04377 | 20230718     |
| TLL04664 | 20240426     |
| TLL02938 | 20240426     |
| TLL03752 | 20230306     |
| TLL04641 | 20231210     |
| TLL04881 | 20230130     |
| TLL05438 | 20240426     |

### 拉取各店最新报货日期

```mysql
-- 直接运行即可
SELECT
  stat_shop_id as 门店编码,
  MAX(business_date) as 上次报货日期
FROM
  ads_dbs_report_food_di
WHERE
  report_amount > 0
GROUP BY
  stat_shop_id;
```

运行结果

| 门店编码 | 上次报货日期 |
| -------- | ------------ |
| ZYD00063 | 20240318     |
| ZYD00041 | 20240426     |
| ZYD00033 | 20230414     |
| TLL05641 | 20240417     |
| TLL05602 | 20240326     |
| TLL05600 | 20240409     |
| TLL05560 | 20240426     |
| TLL05532 | 20240410     |
| TLL05416 | 20240315     |
| TLL05330 | 20240425     |



### 拉取各级经理的报货数据

```mysql
-- 修改日期即可
WITH variables AS (
    SELECT '20240101' AS start_date, '20240323' AS end_date
)
SELECT 
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    region_manager_name AS '大区经理',
    prov_manager_name AS '省区经理',
    district_manager_name AS '区域经理',
    ROUND(SUM(report_amount),2) AS '报货金额'
FROM ads_dbs_report_food_di, variables
WHERE business_date BETWEEN variables.start_date AND variables.end_date
GROUP BY 
    CONCAT(variables.start_date, '~', variables.end_date),
    region_manager_name,
    prov_manager_name,
    district_manager_name;
```

运行结果

| 时段              | 大区经理 | 省区经理 | 区域经理 | 报货金额  |
| ----------------- | -------- | -------- | -------- | --------- |
| 20240401~20240426 | 刘成龙   | 朱迎澳   | 贺横     | 487580.8  |
| 20240401~20240426 | 杨硕     | 裴俊杰   | 丁成龙   | 492261.44 |
| 20240401~20240426 | 胡冰雪   | 刘昊彬   | 毛念秋   | 667283.2  |
| 20240401~20240426 | 杨硕     | 裴俊杰   | 姜生耀   | 585450.4  |
| 20240401~20240426 | 杨硕     | 庞孝笑   | 程博文   | 706845.92 |
| 20240401~20240426 | 赵磊     | 周文君   | 王璐     | 652319.6  |
| 20240401~20240426 | 王枫涛   | 付聪辉   | 陈迪     | 544315.28 |
| 20240401~20240426 | 胡冰雪   | 吴大印   | 吴大印   | 259022.4  |
| 20240401~20240426 | 王枫涛   | 刘紫阳   | 时允诺   | 681536.08 |
| 20240401~20240426 | 刘成龙   | 李何     | 赵杰     | 723976.4  |

### 拉取柠檬橙子的报货周期

- 注：当前仅有柠檬橙子权限，无其它产品权限。

```mysql
-- 直接运行即可
WITH lemon_table AS (
  SELECT
    t1.stat_shop_id,
    t1.lemon_report_cnt,
    t1.business_date AS last_lemon_report_day
  FROM
    ads_dbs_report_food_di t1
    JOIN (
      SELECT
        stat_shop_id,
        MAX(business_date) AS business_date
      FROM
        ads_dbs_report_food_di
      WHERE
        lemon_report_cnt > 0
      GROUP BY
        stat_shop_id
    ) t2 ON t1.stat_shop_id = t2.stat_shop_id
    AND t1.business_date = t2.business_date
  WHERE
    t1.lemon_report_cnt > 0
),
orange_table AS (
  SELECT
    t1.stat_shop_id,
    t1.orange_report_cnt,
    t1.business_date AS last_orange_report_day
  FROM
    ads_dbs_report_food_di t1
    JOIN (
      SELECT
        stat_shop_id,
        MAX(business_date) AS business_date
      FROM
        ads_dbs_report_food_di
      WHERE
        orange_report_cnt > 0
      GROUP BY
        stat_shop_id
    ) t2 ON t1.stat_shop_id = t2.stat_shop_id
    AND t1.business_date = t2.business_date
  WHERE
    t1.orange_report_cnt > 0
)
SELECT
  -- 门店ID
  l.stat_shop_id as 门店编号,
  -- 上次柠檬报货时间
  l.last_lemon_report_day as 上次柠檬报货时间,
  -- 上次柠檬报货数量
  l.lemon_report_cnt as 上次柠檬报货数量,
  -- 上次橙子报货时间
  o.last_orange_report_day as 上次橙子报货时间,
  -- 上次橙子报货数量
  o.orange_report_cnt as 上次橙子报货数量,
  DATEDIFF(CURDATE(), l.last_lemon_report_day) AS 上次柠檬报货距今,
  CASE
    WHEN DATEDIFF(CURDATE(), l.last_lemon_report_day) < 30 THEN '30日内有报货'
    WHEN DATEDIFF(CURDATE(), l.last_lemon_report_day) < 60 THEN '60日内有报货'
    WHEN DATEDIFF(CURDATE(), l.last_lemon_report_day) < 90 THEN '90日内有报货'
    ELSE '90日内无报货'
  END AS 柠檬报货周期,
  DATEDIFF(CURDATE(), o.last_orange_report_day) AS 上次橙子报货距今,
  CASE
    WHEN DATEDIFF(CURDATE(), o.last_orange_report_day) < 30 THEN '30日内有报货'
    WHEN DATEDIFF(CURDATE(), o.last_orange_report_day) < 60 THEN '60日内有报货'
    WHEN DATEDIFF(CURDATE(), o.last_orange_report_day) < 90 THEN '90日内有报货'
    ELSE '90日内无报货'
  END AS 橙子报货周期
FROM
  lemon_table l
  JOIN orange_table o ON l.stat_shop_id = o.stat_shop_id;
```

运行结果

| 门店编号 | 上次柠檬报货时间 | 上次柠檬报货数量 | 上次橙子报货时间 | 上次橙子报货数量 | 上次柠檬报货距今 | 柠檬报货周期 | 上次橙子报货距今 | 橙子报货周期 |
| -------- | ---------------- | ---------------- | ---------------- | ---------------- | ---------------- | ------------ | ---------------- | ------------ |
| TLL04437 | 20230315         | 3                | 20230315         | 2                | 406              | 90日内无报货 | 406              | 90日内无报货 |
| TLL04234 | 20230321         | 2                | 20230321         | 2                | 400              | 90日内无报货 | 400              | 90日内无报货 |
| TLL03600 | 20230407         | 1                | 20230424         | 1                | 383              | 90日内无报货 | 366              | 90日内无报货 |
| TLL03956 | 20230605         | 1                | 20230605         | 1                | 324              | 90日内无报货 | 324              | 90日内无报货 |
| TLL03827 | 20230626         | 1                | 20230626         | 1                | 303              | 90日内无报货 | 303              | 90日内无报货 |
| TLL04940 | 20230804         | 2                | 20230804         | 2                | 264              | 90日内无报货 | 264              | 90日内无报货 |
| TLL03673 | 20230804         | 2                | 20230710         | 3                | 264              | 90日内无报货 | 289              | 90日内无报货 |
| TLL04600 | 20230807         | 3                | 20230807         | 3                | 261              | 90日内无报货 | 261              | 90日内无报货 |
| TLL01918 | 20230807         | 3                | 20230807         | 2                | 261              | 90日内无报货 | 261              | 90日内无报货 |
| TLL01039 | 20230807         | 4                | 20230712         | 4                | 261              | 90日内无报货 | 287              | 90日内无报货 |

### 拉取时间段内，各商品销量及营业额

```mysql
-- 修改日期即可
SELECT
  item_name as 商品名称,
  sum(dp_item_count) as 销售数量,
  sum(dp_total_amount) as 流水金额,
  sum(dp_pay_amount) as 实收金额
FROM
  ads_dbs_trade_food_di
where
  business_date BETWEEN 20240418 AND 20240424
group by
  item_name
```

运行结果

| 商品名称       | 销售数量 | 流水金额   | 实收金额   |
| -------------- | -------- | ---------- | ---------- |
| 原味冰淇淋     | 595027   | 1191220    | 1174023.77 |
| 冰鲜柠檬水     | 587192   | 2421309.34 | 2243327.64 |
| 清风茉白鲜奶茶 | 565192   | 4610631.16 | 4061573.32 |
| 一桶水果茶     | 527273   | 5412502.94 | 4764987.3  |
| 酸奶冰淇淋     | 520341   | 1046891.3  | 972233.72  |
| 其它           | 332417   | 2759822.69 | 1590770.39 |
| 一桶全家福     | 311982   | 3180308.15 | 2938314.28 |
| 葡萄摇摇杯     | 264788   | 2428504.69 | 2292309.86 |
| 葡萄晶球       | 219044   | 1601428.04 | 1404084.73 |
| 黑糖珍珠奶茶   | 198642   | 1425232.52 | 1326560.25 |



### 拉取时间段内，各店的单日营业额

```mysql
-- 修改日期即可
WITH variables AS (
    SELECT '20240401' AS start_date, '20240426' AS end_date
)
SELECT
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    stat_shop_id AS 门店编号,
    business_date AS 日期,
    SUM(total_amount) AS 流水金额,
    SUM(pay_amount) AS 实收金额
FROM
    ads_dbs_trade_shop_di,
    variables
WHERE
    business_date BETWEEN variables.start_date AND variables.end_date
GROUP BY
    时段,
    门店编号,
    日期;
```

运行结果

| 时段              | 门店编号 | 日期     | 流水金额 | 实收金额 |
| ----------------- | -------- | -------- | -------- | -------- |
| 20240401~20240426 | TLL04998 | 20240413 | 885      | 885      |
| 20240401~20240426 | TLL07415 | 20240413 | 2382     | 2031.66  |
| 20240401~20240426 | TLL06157 | 20240413 | 1482     | 1232.8   |
| 20240401~20240426 | TLL07029 | 20240413 | 3301.5   | 2499.88  |
| 20240401~20240426 | TLL02114 | 20240413 | 1506.7   | 1053.28  |
| 20240401~20240426 | TLL06241 | 20240413 | 962.5    | 825.06   |
| 20240401~20240426 | TLL04785 | 20240413 | 2826     | 2481.93  |
| 20240401~20240426 | TLL07350 | 20240413 | 3294.5   | 2894.29  |
| 20240401~20240426 | TLL07499 | 20240413 | 2168.2   | 1877.74  |
| 20240401~20240426 | TLL02938 | 20240413 | 1333.5   | 1074.61  |

### 拉取时间段内，各店流水、实收

```mysql
-- 修改日期即可
WITH variables AS (
    SELECT '20240101' AS start_date, '20240323' AS end_date
)
SELECT
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    stat_shop_id AS 门店编号,
    SUM(total_amount) AS 流水金额,
    SUM(pay_amount) AS 实收金额
FROM
    ads_dbs_trade_shop_di,
    variables
WHERE
    business_date BETWEEN variables.start_date AND variables.end_date
GROUP BY
    时段,
    门店编号;
```

运行结果

| 时段              | 门店编号 | 流水金额  | 实收金额  |
| ----------------- | -------- | --------- | --------- |
| 20240101~20240323 | TLL03864 | 155528.42 | 129338.4  |
| 20240101~20240323 | TLL02114 | 50006.1   | 36014.03  |
| 20240101~20240323 | TLL04785 | 191816.3  | 162131.46 |
| 20240101~20240323 | TLL04644 | 39255.7   | 32080.25  |
| 20240101~20240323 | TLL06250 | 145941.74 | 118885.1  |
| 20240101~20240323 | TLL07350 | 194716.04 | 164296.79 |
| 20240101~20240323 | TLL04664 | 98885.91  | 87927.64  |
| 20240101~20240323 | TLL06568 | 26082     | 26082     |
| 20240101~20240323 | TLL07499 | 140604.8  | 113842.14 |
| 20240101~20240323 | TLL05338 | 185029.1  | 164060.9  |

增加**营业天数**就比较麻烦

### 拉取时间段内，各店流水、实收、营业天数

```mysql
-- 修改日期即可
WITH time_period AS (
    SELECT '20240401' AS start_date, '20240427' AS end_date
)
SELECT
    CONCAT(time_period.start_date, '~', time_period.end_date) AS 时段,
    门店编码,
    SUM(流水金额) AS 总流水金额,
    SUM(实收金额) AS 总实收金额,
    SUM(营业天数) AS 总营业天数
FROM
    (SELECT
        business_date AS 日期,
        stat_shop_id AS 门店编码,
        SUM(total_amount) AS 流水金额,
        SUM(pay_amount) AS 实收金额,
        CASE
            WHEN SUM(total_amount) > 0 THEN 1
            ELSE 0
        END AS 营业天数
    FROM
        ads_dbs_trade_shop_di,time_period
    WHERE
        business_date BETWEEN time_period.start_date AND time_period.end_date
    GROUP BY
        门店编码,
        business_date) AS day_table,
    time_period
GROUP BY
    时段,
    门店编码;
```



| 时段              | 门店编码 | 总流水金额 | 总实收金额 | 总营业天数 |
| ----------------- | -------- | ---------- | ---------- | ---------- |
| 20240401~20240427 | TLL06506 | 32293.4    | 28590.1    | 27         |
| 20240401~20240427 | TLL04644 | 26897.04   | 20725.56   | 27         |
| 20240401~20240427 | TLL07350 | 81876      | 73637.87   | 26         |
| 20240401~20240427 | TLL07213 | 34614.08   | 29555.21   | 26         |
| 20240401~20240427 | TLL07449 | 24218      | 20767.49   | 26         |
| 20240401~20240427 | TLL06271 | 27056.9    | 22723.46   | 25         |
| 20240401~20240427 | TLL05438 | 16803.8    | 14341.17   | 26         |
| 20240401~20240427 | TLL04202 | 66291.1    | 56733.79   | 27         |
| 20240401~20240427 | TLL04939 | 0.00E+00   | 0.00E+00   | 0          |
| 20240401~20240427 | TLL05504 | 44977.5    | 41269.79   | 27         |

###  ★拉取时间段内，各店各渠道收银

```mysql
WITH variables AS (
    SELECT '20240101' AS start_date, '20240426' AS end_date
)
SELECT
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    stat_shop_id AS 门店编码,
    platform AS 渠道,
    SUM(total_amount) AS 流水金额
FROM
    ads_dbs_trade_shop_di, variables
WHERE
    business_date BETWEEN variables.start_date AND variables.end_date
GROUP BY
    时段,
    stat_shop_id,
    platform;
```

运行结果

| 时段              | 门店编码 | 渠道   | 流水金额  |
| ----------------- | -------- | ------ | --------- |
| 20240101~20240426 | TLL06223 | 美团   | 27425.8   |
| 20240101~20240426 | TLL04998 | 美团   | 41        |
| 20240101~20240426 | TLL07415 | 小程序 | 6192.5    |
| 20240101~20240426 | TLL07415 | pos    | 94793.3   |
| 20240101~20240426 | TLL07029 | 饿了么 | 11486.8   |
| 20240101~20240426 | TLL03864 | pos    | 119806.62 |
| 20240101~20240426 | TLL03864 | 饿了么 | 10468.4   |
| 20240101~20240426 | TLL03680 | 美团   | 0.00E+00  |
| 20240101~20240426 | TLL03680 | pos    | 0.00E+00  |
| 20240101~20240426 | TLL02135 | pos    | 137327.2  |

透视一下即可

| 门店编码 | pos       | 饿了么   | 美团      | 其它 | 小程序  | 总计      |
| -------- | --------- | -------- | --------- | ---- | ------- | --------- |
| TLL00001 | 153089    | 0        | 0         | 0    | 5469    | 158558    |
| TLL00004 | 237490.6  | 0        | 0         | 0    | 9336.4  | 246827    |
| TLL00006 | 270954.45 | 35590.2  | 135377.35 | 0    | 10723.2 | 452645.2  |
| TLL00007 | 53208.4   | 0        | 34377     | 0    | 3287    | 90872.4   |
| TLL00008 | 62936.8   | 9625.49  | 20738.29  | 0    | 2362    | 95662.58  |
| TLL00009 | 291771.44 | 130      | 3741.01   | 0    | 10047   | 305689.45 |
| TLL00010 | 118816.7  | 14058.44 | 16620.8   | 0    | 4561    | 154056.94 |
| TLL00012 | 151120.06 | 12120.97 | 0         | 0    | 7589.5  | 170830.53 |
| TLL00013 | 167357.6  | 0        | 8017      | 0    | 23877   | 199251.6  |
| TLL00014 | 314589.9  | 0        | 0         | 0    | 45652.5 | 360242.4  |
#### ★日常使用

渠道表：获取时间段内，各店营业天数、营业额、 营业收入、各渠道流水、实收、单量。

STEP1:

```
WITH variables AS (
    SELECT '20230501' AS start_date, '20230517' AS end_date
)
SELECT
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    business_date as 日期,
    stat_shop_id AS 门店编码,
    platform AS 渠道,
    SUM(total_amount) AS 流水金额
FROM
    ads_dbs_trade_shop_di, variables
WHERE
    business_date BETWEEN variables.start_date AND variables.end_date
GROUP BY
    时段,
    business_date,
    stat_shop_id,
    platform;
```

STEP2：

进入：渠道销售数据格式化 脚本。

# 美团收银系统

当前收银系统为美团管家，https://pos.meituan.com/  。

# 哗啦啦收银系统

2023年及以前，收银系统为[https://passport.hualala.com](https://passport.hualala.com/login?redirectURL=http%3A%2F%2Fshop.hualala.com)

# 自动化脚本

点击链接获取 https://note.bizha.top/tianlala 获取主页链接。由python flask制作。使用脚本前可先查看“已生成的文件列表”，是否有小伙伴已经生成，可直接下载使用。

## 新品报货（销售）

主要用于制作各个加盟店新品的报货（销售）情况报货。

结果示例（未导入销售表）

| 门店编码 | 门店名称                                           | 大区经理 | 省区经理 | 区域经理 | 南北战区 | 运营状态 | 省   | 市     | 区       | U8C客商编码 | 冷冻牛油果泥报货数量 | 报货情况 |
| -------- | -------------------------------------------------- | -------- | -------- | -------- | -------- | -------- | ---- | ------ | -------- | ----------- | -------------------- | -------- |
| TLL07942 | 凯立花园店                                         | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 大连市 | 瓦房店市 | 2010572     | 0                    | 无报货   |
| TLL07863 | 阜新辽工大                                         | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 阜新市 | 细河区   | 2010390     | 0                    | 无报货   |
| TLL07662 | 大连大学生活服务中心二楼                           | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 金州区   | 2010285     | 2                    | 有报货   |
| TLL07586 | 营城子镇润泰商城                                   | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 甘井子区 | 2010253     | 2                    | 有报货   |
| TLL07348 | 沙河口区中山路星海广场                             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 沙河口区 | 2010038     | 0                    | 无报货   |
| TLL07333 | 辽宁大连市甘井子区泉水D1区10-7号甜啦啦             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 大连市 | 甘井子区 | 2010090     | 0                    | 无报货   |
| TLL07291 | 大连市卧龙园市场甜啦啦店                           | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 金州区   | 2010057     | 0                    | 无报货   |
| TLL07255 | 大连市旅顺口区大连外国语大学(旅顺校区)校外海鲜街店 | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 旅顺口区 | 2009852     | 0                    | 无报货   |
| TLL07176 | 辽宁盘锦市兴隆台区东方托莱多2号商网107             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 盘锦市 | 兴隆台区 | 2009540     | 0                    | 无报货   |
| TLL07010 | 瓦房店西口大商店                                   | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 瓦房店市 | 2009256     | 0                    | 无报货   |

结果示例（导入销售表）：

| 门店编码 | 门店名称                                       | 大区经理 | 省区经理 | 区域经理 | 南北战区 | 运营状态 | 省   | 市     | 区     | U8C客商编码 | 碧玉桃花组合套装报货数量 | 碧玉桃花鲜奶茶销售数量 | 报货销售情况 |
| -------- | ---------------------------------------------- | -------- | -------- | -------- | -------- | -------- | ---- | ------ | ------ | ----------- | ------------------------ | ---------------------- | ------------ |
| TLL07557 | 淮北市烈山区荣盛花园生活广场一号楼17-18号      | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 淮北市 | 烈山区 | 2009650     | 1                        | 196                    | 有报货有销售 |
| TLL07184 | 安徽省毫州市利辛县孙集镇十字街北10米路西甜啦啦 | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 亳州市 | 利辛县 | 2009413     | 1                        | 89                     | 有报货有销售 |
| TLL07118 | 亳州市利辛县巩店镇初级中学斜对面               | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 亳州市 | 利辛县 | 2009356     | 1                        | 97                     | 有报货有销售 |
| TLL06921 | 安徽淮北市相山区大华新家园北门                 | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 淮北市 | 相山区 | 2008954     | 1                        | 128                    | 有报货有销售 |
| TLL06235 | 淮北市烈山区古饶镇新风花园门口                 | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 淮北市 | 烈山区 | 2008008     | 1                        | 134                    | 有报货有销售 |
| TLL05763 | 安徽亳州市利辛县纪王场乡中心街道甜啦啦店       | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 亳州市 | 利辛县 | 2007833     | 1                        | 9                      | 有报货有销售 |
| TLL05657 | 利辛县城关镇七彩世界四楼美食广场               | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 亳州市 | 利辛县 | 2007653     | 1                        | 224                    | 有报货有销售 |
| TLL05458 | 利辛县前进路月亮湾小区南门                     | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 长期闭店 | 安徽 | 亳州市 | 利辛县 | 2007420     | 0                        | 0                      | 无报货无销售 |
| TLL05212 | 孙庙乡店                                       | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 亳州市 | 利辛县 | 2007237     | 1                        | 190                    | 有报货有销售 |
| TLL04896 | 淮北市杜集区高岳街道青年路淮北理工学院         | 邵陈龙   | 谢鑫     | 陈特     | 北方战区 | 营业中   | 安徽 | 淮北市 | 杜集区 | 2005603     | 1                        | 188                    | 有报货有销售 |





## 单品报货（90天）

主要用于制作各个加盟店柠檬、橙子、调味糖浆等产品的报货情况。根据上次报货时间，判断“30日内有报货”、“60日内有报货”、“90日内有报货”，“90日内无报货”。

结果示例：

| 门店编码 | 门店名称                                           | 大区经理 | 省区经理 | 区域经理 | 南北战区 | 运营状态 | 省   | 市     | 区       | U8C客商编码 | 上次报货日期 | 橙子数量 | 上次报货距今 | 橙子报货周期 |
| -------- | -------------------------------------------------- | -------- | -------- | -------- | -------- | -------- | ---- | ------ | -------- | ----------- | ------------ | -------- | ------------ | ------------ |
| TLL07942 | 凯立花园店                                         | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 大连市 | 瓦房店市 | 2010572     |              | -        | -            | 90日内无报货 |
| TLL07863 | 阜新辽工大                                         | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 阜新市 | 细河区   | 2010390     |              | -        | -            | 90日内无报货 |
| TLL07662 | 大连大学生活服务中心二楼                           | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 金州区   | 2010285     | 2024/3/19    | 1        | 7            | 30日内有报货 |
| TLL07586 | 营城子镇润泰商城                                   | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 甘井子区 | 2010253     | 2024/3/20    | 3        | 6            | 30日内有报货 |
| TLL07348 | 沙河口区中山路星海广场                             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 沙河口区 | 2010038     | 2024/3/18    | 3        | 8            | 30日内有报货 |
| TLL07333 | 辽宁大连市甘井子区泉水D1区10-7号甜啦啦             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 签约     | 辽宁 | 大连市 | 甘井子区 | 2010090     |              | -        | -            | 90日内无报货 |
| TLL07291 | 大连市卧龙园市场甜啦啦店                           | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 金州区   | 2010057     | 2024/3/18    | 1        | 8            | 30日内有报货 |
| TLL07255 | 大连市旅顺口区大连外国语大学(旅顺校区)校外海鲜街店 | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 旅顺口区 | 2009852     |              | -        | -            | 90日内无报货 |
| TLL07176 | 辽宁盘锦市兴隆台区东方托莱多2号商网107             | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 盘锦市 | 兴隆台区 | 2009540     |              | -        | -            | 90日内无报货 |
| TLL07010 | 瓦房店西口大商店                                   | 刘向阳   | 刘波     | 乔猛     | 北方战区 | 营业中   | 辽宁 | 大连市 | 瓦房店市 | 2009256     |              | -        | -            | 90日内无报货 |

## 单品销售统计

主要用于制作时间段内，加盟店有无该菜品销售的报表。

结果示例：

| 门店编码 | 门店名称                                               | 大区经理 | 省区经理 | 区域经理 | 南北战区 | 运营状态 | 省   | 市       | 区     | U8C客商编码 | 销售数量 | 碧玉销售 |
| -------- | ------------------------------------------------------ | -------- | -------- | -------- | -------- | -------- | ---- | -------- | ------ | ----------- | -------- | -------- |
| TLL08003 | 河南平顶山市卫东区万达广场3楼                          | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 签约     | 河南 | 平顶山市 | 卫东区 | 2010629     | 0        | 无销售   |
| TLL07961 | 河南平顶山市湛河区湛南路西苑小区北门往西200米          | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 签约     | 河南 | 平顶山市 | 湛河区 | 2010560     | 0        | 无销售   |
| TLL07855 | 翠湖苑小区                                             | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2010482     | 0        | 无销售   |
| TLL07458 | 河南平顶山市湛河区河南质量工程学院大门右侧第一家甜啦啦 | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2010196     | 0        | 无销售   |
| TLL06442 | 河南工业职业技术学院餐厅一楼                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2008606     | 0        | 无销售   |
| TLL06401 | 新华路锦绣小区                                         | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2008578     | 1        | 有销售   |
| TLL06030 | 平顶山市新华区开源商贸城旁店                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2008127     | 0        | 无销售   |
| TLL05856 | 未来路综合市场                                         | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2007898     | 0        | 无销售   |
| TLL05392 | 林溪谷甜啦啦                                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2007369     | 22       | 有销售   |



## 套餐菜品名称还原

主要用于还原**套餐菜品名称**为**标准菜品名称**，用于统计菜品销量。



## 同环比期计算

通过本期，计算同比期、环比期时段。注：**只可计算日期维度。**

##  监控在线统计

统计排除空合同、长期闭店

结果示例：

| 大区经理 | 省区经理 | 区域经理 | 监控门店数 | 在线 | 部分在线 | 离线 | 监控门店数(营业中) | 在线(营业中) | 部分在线(营业中) | 离线(营业中) |
| -------- | -------- | -------- | ---------- | ---- | -------- | ---- | ------------------ | ------------ | ---------------- | ------------ |
| 严鹤     | 侯政权   | 付成浩   | 22         | 3    | 7        | 12   | 21                 | 3            | 7                | 11           |
| 严鹤     | 侯政权   | 侯政权   | 17         | 8    | 4        | 5    | 17                 | 8            | 4                | 5            |
| 严鹤     | 侯政权   | 刘侨飞   | 23         | 7    | 3        | 13   | 23                 | 7            | 3                | 13           |
| 严鹤     | 侯政权   | 邵明俊   | 22         | 11   | 3        | 8    | 21                 | 11           | 3                | 7            |
| 严鹤     | 侯政权   | 陈旭辉   | 22         | 11   | 6        | 5    | 22                 | 11           | 6                | 5            |
| 严鹤     | 郜朦     | 刘伟豪   | 18         | 1    | 4        | 13   | 18                 | 1            | 4                | 13           |
| 严鹤     | 郜朦     | 吴宇航   | 26         | 12   | 1        | 13   | 25                 | 12           | 1                | 12           |
| 严鹤     | 郜朦     | 赵鹏     | 23         | 7    | 2        | 14   | 22                 | 7            | 2                | 13           |
| 严鹤     | 郜朦     | 郜朦     | 24         | 8    | 1        | 15   | 21                 | 8            | 1                | 12           |
| 严鹤     | 陈冬冬   | 刘军旗   | 13         | 2    | 3        | 8    | 13                 | 2            | 3                | 8            |

## 门店管理表格式化

对门店管理表进行简单处理，通常用于中间过程。

| 门店编码 | 门店名称                                               | 大区经理 | 省区经理 | 区域经理 | 南北战区 | 运营状态 | 省   | 市       | 区     | U8C客商编码 |
| -------- | ------------------------------------------------------ | -------- | -------- | -------- | -------- | -------- | ---- | -------- | ------ | ----------- |
| TLL08003 | 河南平顶山市卫东区万达广场3楼                          | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 签约     | 河南 | 平顶山市 | 卫东区 | 2010629     |
| TLL07961 | 河南平顶山市湛河区湛南路西苑小区北门往西200米          | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2010560     |
| TLL07855 | 翠湖苑小区                                             | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2010482     |
| TLL07458 | 河南平顶山市湛河区河南质量工程学院大门右侧第一家甜啦啦 | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2010196     |
| TLL06442 | 河南工业职业技术学院餐厅一楼                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2008606     |
| TLL06401 | 新华路锦绣小区                                         | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2008578     |
| TLL06030 | 平顶山市新华区开源商贸城旁店                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2008127     |
| TLL05856 | 未来路综合市场                                         | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 新华区 | 2007898     |
| TLL05392 | 林溪谷甜啦啦                                           | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 营业中   | 河南 | 平顶山市 | 湛河区 | 2007369     |
| TLL04953 | 矿工路和新华路                                         | 严鹤     | 侯政权   | 付成浩   | 北方战区 | 短期闭店 | 河南 | 平顶山市 | 卫东区 | 2004037     |

## 会员日销售数据处理

用于处理会员日销售信息基本处理，一般导入改日冰鲜柠檬水、冰淇淋的销售详单。

处理逻辑：遍历详单，订单子来源为“店内点餐”且“优惠金额”>=2元，视为优惠订单。

结果示例：

| 机构编码 | 销售数量_总 | 销售数量_优惠 |
| -------- | ----------- | ------------- |
| TLL00001 | 9           | 0             |
| TLL00004 | 15          | 7             |
| TLL00006 | 23          | 0             |
| TLL00007 | 4           | 0             |
| TLL00008 | 5           | 0             |
| TLL00009 | 12          | 0             |
| TLL00010 | 12          | 1             |
| TLL00012 | 18          | 11            |
| TLL00013 | 6           | 1             |
| TLL00014 | 18          | 3             |



# EXCEL

## 样式





# 其它

- [甜啦啦蚌埠地区直营店分布](http://tll.bizha.top/tll_zhiyin.html)

# TODO

- [x] 新品报货 脚本制作

- [x] 单品报货 脚本制作
- [x] 单品销售统计 脚本制作
- [x] 套餐菜品名称还原 脚本制作
- [x] 中台mysql使用示例
- [ ] 报表格式规则 编写

