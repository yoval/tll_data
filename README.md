# 基础概念

- 同比

  与历史“同时期”比较，例如2011年3月份与2010年3月份相比，叫“同比”，“同比期”又称“同期”。

- 环比

  与“上一个”统计周期比较，例如2011年4月份与2011年3月份相比较，称为“环比”。

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
| 040001537 | 2024版清风茉白-B套餐   |           |  (旧) |
| 120001549 | 牛油果宣传物料包       | 2024/3/23 |        |
| 040000807 | 冷冻牛油果泥           | 2024/3/23 |        |
| 060000019 | PLA粗吸管              |           |        |
| 060000020 | PLA细吸管              |           |        |
| 060000889 | PLA粗吸管（黑）        |           |        |
| 040001410 | 龙麟香组合套装         | 2024/1/6  |        |
| 040000983 | 速冻桑葚浆             |           |        |
| 120001554 | 超仙黑桑葚宣传物料包   | 2024/4/9  | 4800套 |
| 040001555 | 2024版桑葚包材套装     | 2024/4/9  | 4500套 |

# 中台的使用

中台的地址为：https://data.tianlala.com/ 

**注：**

1. 中台数据每天凌晨更新，非即时数据。即时数据应到相应平台提取。

2. 中台采用Mysql数据库，因使用mysql语句。

   

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

主要用于查询门店报货信息，能够查询**单店单日报货金额（已去除首批，未还原二八分账）**。查询不了详细订单、单品报货等内容。

例：

```mysql
SELECT LEFT(business_date, 6) AS 月份, stat_shop_name AS 门店名称, SUM(report_amount)/0.8 AS 报货金额
FROM ads_dbs_report_food_di
WHERE stat_shop_id = 'TLL03855'
GROUP BY 门店名称, 月份
```

查询门店TLL03855各月报货信息，表`ads_dbs_report_food_di`包含字段如下：

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

主要用于查询单店单日各渠道销售情况，如：

```mysql
SELECT LEFT(business_date, 6) AS 月份, stat_shop_name AS 门店名称,platform as 平台, SUM(total_amount) AS 流水金额
FROM ads_dbs_trade_shop_di
WHERE stat_shop_id = 'TLL03855'
GROUP BY 门店名称, 平台,月份
```

查询门店TLL03855各月各平台的销售情况，表`ads_dbs_trade_shop_di`包含字段如下：

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

主要用于查询单店单日单品销售情况，如：

```mysql
SELECT LEFT(business_date, 6) AS 月份, stat_shop_name AS 门店名称,platform as 平台, SUM(total_amount) AS 流水金额
FROM ads_dbs_trade_food_di
WHERE stat_shop_id = 'TLL03855'
GROUP BY 门店名称, 平台,月份
```

查询门店TLL03855各月各平台的销售情况，表`ads_dbs_trade_shop_di`包含字段如下：

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
SELECT LEFT(business_date, 6) AS 月份, stat_shop_name AS 门店名称, SUM(total_amount) AS 流水金额,sum(pay_amount) as 实收金额
FROM ads_dbs_trade_shop_di
WHERE stat_shop_id = 'TLL02973'
GROUP BY 门店名称,月份
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

### 拉取各店各月销售、报货数据

```mysql
SELECT
  LEFT(business_date, 6) AS 月份,
  stat_shop_id AS 门店编号,
  SUM(total_amount) AS 流水金额,
  SUM(report_amount) / 0.8 AS 报货金额
FROM
  ads_dbs_report_food_di
GROUP BY
  门店编号,
  月份
```



运行结果

| 月份   | 门店编号 | 流水金额 | 报货金额 |
| ------ | -------- | -------- | -------- |
| 202311 | TLL00001 | 2166     | 1636     |
| 202311 | TLL00004 | 1868     | 583      |
| 202311 | TLL00006 | 6743     | 4108     |
| 202311 | TLL00007 | 1031     | 2658     |
| 202311 | TLL00008 | 2200     | 1390     |
| 202311 | TLL00012 | 1561     | 1478     |
| 202311 | TLL00013 | 5097     | 2671     |
| 202311 | TLL00014 | 4760     | 4494     |
| 202311 | TLL00022 | 680      | 1177     |
| 202311 | TLL00023 | 8        | 954      |
| 202311 | TLL00024 | 2099     | 2965     |



### 拉取各级经理的报货数据

```mysql
WITH variables AS (
    SELECT '20240101' AS start_date, '20240323' AS end_date
)
SELECT 
    CONCAT(variables.start_date, '~', variables.end_date) AS 时段,
    region_manager_name AS '大区经理',
    prov_manager_name AS '省区经理',
    district_manager_name AS '区域经理',
    ROUND(SUM(total_amount),2) AS '流水金额',
    ROUND(SUM(pay_amount),2) AS '实收金额',
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

| 时段                                                         | 大区经理 | 省区经理 | 区域经理 | 流水金额 | 实收金额 | 报货金额 |
| ------------------------------------------------------------ | -------- | -------- | -------- | -------- | -------- | -------- |
| 20240101~20240323                                            | 刘成龙   | 朱迎澳   | 康子龙   | 684.4    | 5.77     | 628.8    |
| 20240101~20240323                                            | 刘向阳   | 刘波     | 刘波     | 49.82    | 361.36   | 19.2     |
| 202024版桑葚包材套装报货2024版桑葚包材套装报货240101~20240323 | 胡冰雪   | 吴大印   | 刘远     | 418.25   | 200592.7 | 175.68   |
| 20240101~20240323                                            | 邵陈龙   | 张良崎   | 王鹏翔   | 47.01    | 30.07    | 112.0    |
| 20240101~20240323                                            | 赵磊     | 魏磊     | 刘春贤   | 1474.6   | 1405.93  | 224.0    |
| 20240101~20240323                                            | 王枫涛   | 毛阳     | 徐博文   | 408.9    | 19.06    | 224.8    |
| 20240101~20240323                                            | 王枫涛   | 韩善武   | 董薛彪   | 515.67   | 44.91    | 71.2     |
| 20240101~20240323                                            | 赵磊     | 侯政权   | 赵哲     | 374.6    | 39.47    | 109.2    |
| 20240101~20240323                                            | 王枫涛   | 邓斌     | 马亚军   | 7.25     | 36.73    | 597.6    |

### 查询柠檬香橙报货及当月流水

```mysql
SELECT
  t1.门店编号,
  t1.上次橙子报货时间,
  t1.上次柠檬报货时间,
  t2.流水金额
FROM
  (SELECT
    stat_shop_id AS 门店编号,
    MAX(CASE WHEN orange_report_cnt > 0 THEN business_date END) AS 上次橙子报货时间,
    MAX(CASE WHEN is_lemon_report > 0 THEN business_date END) AS 上次柠檬报货时间
  FROM
    ads_dbs_report_food_di
  GROUP BY
    stat_shop_id) AS t1
JOIN
  (SELECT
    stat_shop_id AS 门店编号,
    LEFT(business_date, 6) AS 月份,
    SUM(total_amount) AS 流水金额
  FROM
    ads_dbs_report_food_di
  WHERE
    LEFT(business_date, 6) = '202404'
  GROUP BY
    stat_shop_id, 月份) AS t2
ON
  t1.门店编号 = t2.门店编号;
```

运营结果

| 门店编号 | 上次橙子报货时间 | 上次柠檬报货时间 | 流水金额 |
| -------- | ---------------- | ---------------- | -------- |
| TLL05553 | 20240412         | 20240412         | 2005     |
| TLL05447 | 20240411         | 20240411         | 4201     |
| TLL05384 | 20240415         | 20240415         | 612      |
| TLL05282 | 20240417         | 20240330         | 2743.6   |
| TLL05250 | 20240410         | 20240410         | 1143     |
| TLL05210 | 20240415         | 20240415         | 3077.2   |
| TLL05088 | 20240415         | 20240415         | 2590.4   |
| TLL05072 | 20240402         | 20240402         | 4635.9   |



# 美团收银系统

当前收银系统为美团管家，https://pos.meituan.com/  。

# 哗啦啦收银系统

2023年及以前，收银系统为[https://passport.hualala.com](https://passport.hualala.com/login?redirectURL=http%3A%2F%2Fshop.hualala.com)

# 自动化脚本

自动化脚本主页为http://10.10.3.61:5000/ （内网），由python flask制作。使用脚本前可先查看“已生成的文件列表”，是否有小伙伴已经生成，可直接下载使用。

## 新品报货（销售）

主要用于制作各个加盟店新品的报货（销售）情况报货。

需求表格：

- （必选）门店管理文件

  识别的是微协同→客户管理→门店管理→门店-可导出，点击导出，导出全部字段。

- （必选）U8C导出文件

  识别的是通过“预览”→“导出excel”所导出的`.xls`格式文件，应通过**存货编码**进行查询。

- （可选）新品销售文件

  识别的是美团管家→菜品销售统计生成的文件。

如果没有“新品销售”文件，生成的表格判断逻辑是“有报货”、“无报货”；如果有“新品销售”文件，生成的表格判断逻辑是“有销售有报货”、“有销售无报货”、“无销售有报货”、“无销售无报货”

## 单品报货（90天）

主要用于制作各个加盟店柠檬、橙子、调味糖浆等产品的报货情况。根据上次报货时间，判断“30日内有报货”、“60日内有报货”、“90日内有报货”，“90日内无报货”。

需求表格：

- （必选）门店管理文件

  同上

- （必选）U8C导出文件

  同上

## 单品销售统计

主要用于制作时间段内，加盟店有无该菜品销售的报表。

需求表格：

- （必选）门店管理文件

  同上

- （必选）菜品销售统计

  美团菜品销售统计表

## 套餐菜品名称还原

主要用于还原**套餐菜品名称**为**标准菜品名称**，用于统计菜品销量。

需求表格：

- （必选）菜品销售表

  可识别美团导出的菜品销售文件，也可以识别表头含“菜品名称”的文件。

- （必选）菜品映射表

  识别的是微协同→报表中心→报表分析，门店栏的“菜品名称对照登记查询”。该表由线上部门维护。


# 其它

- [甜啦啦蚌埠地区直营店分布](http://tll.bizha.top/tll_zhiyin.html)

# TODO

- [x] 新品报货 脚本制作

- [x] 单品报货 脚本制作
- [x] 单品销售统计 脚本制作
- [x] 套餐菜品名称还原 脚本制作
- [x] 中台mysql使用示例
- [ ] 报表格式规则 编写

