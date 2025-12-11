# 数据的单元和集成测试方案

本文档描述「个人工作台」模块的数据单元测试和集成测试方案，包括测试方法、测试用例、测试数据准备和自动化测试脚本等内容。

---

## 一、测试概述

### 1.1 测试目标

本文档针对「个人工作台」模块编写全面的接口测试方案，采用数据驱动的接口测试方法，确保功能正确性和数据权限控制的准确性。

### 1.2 被测试系统说明

个人工作台是系统的入口页面，为不同角色提供指令处理和通知查看功能。工作台作为一级目录，包含指令工作台和通知列表两个二级目录。

### 1.3 测试范围

本测试方案覆盖以下功能模块的接口测试：
- 一级目录权限控制
- 指令工作台功能
- 通知列表功能
- 界面状态保存与恢复
- 手机分辨率适配
- 数据权限过滤

---

## 二、被测试系统功能说明（测试背景）

以下章节说明被测试系统的功能需求，作为测试用例设计的依据。

### 2.1 目录结构

- **一级目录**：工作台
- **二级目录**：
  - 指令工作台（根据当前登录人员的角色决定有哪些三级目录）
  - 通知列表

---

### 2.2 一级目录内容筛选规则

根据当前登录用户的角色，系统自动筛选显示的一级目录内容：

#### 2.2.1 超级管理员

- **显示所有一级目录**：
  - 合同宝
  - 指令宝
  - 匹配宝
  - 交易宝
  - 风控宝
  - 报表
  - 全局设置

#### 2.2.2 交易员

- **显示一级目录**：
  - 交易宝

#### 2.2.3 风控员

- **显示一级目录**：
  - 风控宝
  - 报表（其他报表）

#### 2.2.4 匹配员

- **显示一级目录**：
  - 匹配宝

#### 2.2.5 制单员

- **显示一级目录**：
  - 合同宝

#### 2.2.6 指令员

- **显示一级目录**：
  - 指令宝

#### 2.2.7 业务经理

- **显示一级目录**：
  - 合同宝（仅显示合同中业务经理是当前用户的合同）

#### 2.2.8 角色权限说明

- 用户可能拥有多个角色，系统取并集显示所有角色对应的一级目录
- 数据权限按角色和数据范围过滤（如业务经理只看自己负责的合同）
- 一级目录的显示/隐藏由后端根据用户角色动态返回

---

### 2.3 指令工作台

#### 2.3.1 目录路径

- 一级：工作台
- 二级：指令工作台
- 三级：根据当前登录人员的角色决定

#### 2.3.2 三级目录列表

根据当前登录用户的角色，系统显示对应的指令工作台三级目录：

##### 2.3.2.1 业务经理工作台

- **显示条件**：用户拥有"业务经理"角色
- **功能说明**：业务经理可以发起指令、查看自己发起的指令、查看与自己相关的指令
- **详细功能**：参考指令宝模块中"业务经理指令发起"相关文档

##### 2.3.2.2 交易员工作台

- **显示条件**：用户拥有"交易员"角色
- **功能说明**：交易员可以发起交易指令、认领交易指令、查看所有交易指令
- **详细功能**：参考指令宝模块中"交易员指令发起"相关文档

##### 2.3.2.3 指令员工作台

- **显示条件**：用户拥有"指令员"角色
- **功能说明**：指令员可以发起指令、查看所有指令（包括均价指令自动发起、变更、新增导致的指令变化）
- **详细功能**：参考指令宝模块中"指令员指令发起"相关文档

##### 2.3.2.4 制单员工作台

- **显示条件**：用户拥有"制单员"角色
- **功能说明**：制单员可以查看需要制单的指令、完成合同创建
- **详细功能**：参考指令宝模块中"制单员创建合同"相关文档

##### 2.3.2.5 客商工作台

- **显示条件**：用户拥有"客商"角色
- **功能说明**：客商可以发起期货交易指令、查看自己发起的指令
- **详细功能**：参考指令宝模块中"客商指令权限设置"相关文档

#### 2.3.3 三级目录显示规则

- 系统根据当前登录用户的角色，动态显示对应的三级目录
- 如果用户拥有多个角色，则显示多个对应的三级目录
- 三级目录的显示/隐藏由后端根据用户角色动态返回
- 点击三级目录进入对应的指令工作台页面

#### 2.3.4 数据权限

- 指令工作台中的数据按用户角色和数据权限过滤
- 业务经理：只看自己发起的指令和与自己相关的指令
- 交易员：可以查看所有交易指令，但只能认领自己有权限的指令
- 指令员：可以查看所有指令
- 制单员：只看需要制单的指令
- 客商：只看自己发起的指令

---

### 2.4 通知列表

#### 2.4.1 目录路径

- 一级：工作台
- 二级：通知列表

#### 2.4.2 功能说明

通知列表统一展示所有由"风控设置"中配置的异常提醒规则触发生成的告警记录，以及被转发的通知记录。

#### 2.4.3 数据来源

##### 2.4.3.1 告警转通知

- 来源于风控宝告警记录中被"转通知"的记录
- 当告警记录被"转通知"后，会在被通知人员的工作台"通知列表"中生成一条通知记录
- 通知记录与告警记录关联，但展示方式更简洁（适合工作台快速查看）

##### 2.4.3.2 系统通知

- 系统公告
- 任务分派
- 其他系统级通知

#### 2.4.4 列表字段

- 通知时间
- 通知编号（系统自动生成）
- 通知类型（告警转通知 / 系统通知）
- 通知标题
- 通知内容（简要）
- 来源（如：风控告警、系统公告）
- 处理状态（未处理 / 已处理 / 关注中）
- 处理人
- 最近处理时间
- 备注（显示最近几条处理记录摘要）

#### 2.4.5 筛选条件

- **时间范围**：支持按通知时间区间筛选（近 1 日/近 7 日/近 30 日/自定义）
- **通知类型**：多选（告警转通知 / 系统通知）
- **处理状态**：未处理 / 已处理 / 关注中
- **来源**：风控告警、系统公告等

#### 2.4.6 处理操作

##### 2.4.6.1 支持的处理动作

**【关注】**
- 表示该通知需要持续关注，暂不关闭
- 操作后状态变为"关注中"
- 操作会记录到告警记录的备注中，格式如：张三 关注

**【已处理】**
- 表示已确认并完成相应业务/风控动作
- 操作后状态变为"已处理"
- 操作会记录到告警记录的备注中，格式如：张三 已处理

**【转通知】**
- 将该通知信息转发给某一或多位用户
- 转发后，被转发的用户在其工作台的"通知列表"中可以看到该通知
- 操作会记录到告警记录的备注中，格式如：张三 转通知 李四

**批量处理**：支持批量处理，多选后点按钮操作

##### 2.4.6.2 与风控告警记录的联动

- 在通知列表界面点击"关注/已处理/转通知"，操作会记录到告警记录的备注中
- 两个界面的操作状态保持同步
- 从通知列表可以跳转到"风控告警记录"的详情页查看完整信息

#### 2.4.7 红点提醒

- 记录上一次页面打开，显示到哪一条通知记录
- 之后有新来的通知记录，会在二级目录上显示一个红点
- 点开通知列表，会在上次之后的条目上显示new
- 以此反复，这个记录应该记在期现服务器上的
- 参考风控宝6.10章节的红点提醒规则

---

### 2.5 手机分辨率适配

#### 2.5.1 设备判断

- 系统只判断一个分辨率界限来区分设备类型（参考0_系统缺省默认.md）
- 大于等于该界限：使用PC端布局
- 小于该界限：使用移动端布局

#### 2.5.2 手机端一级目录显示

- **手机分辨率登录时，只提供以下一级目录**：
  - 工作台（包含指令工作台和通知列表）
  - 交易宝（需要做一个手机分辨率适配的交易宝）

#### 2.5.3 手机端工作台内容

- **指令工作台**：根据用户角色显示对应的三级目录
- **通知列表**：完整功能，支持查看和处理通知

#### 2.5.4 手机端交易宝适配

- **需要做一个手机分辨率适配的交易宝**
- 功能要求：
  - 支持查看行情
  - 支持下单操作
  - 支持查看持仓
  - 支持查看账户信息
  - 界面布局适配手机屏幕
  - 操作流程简化，适合移动端使用

#### 2.5.5 手机端界面布局

- 采用移动端友好的布局方式
- 列表采用卡片式展示
- 操作按钮采用底部固定或悬浮按钮
- 支持下拉刷新
- 支持上拉加载更多

---

### 2.6 数据来源和权限

#### 2.6.1 数据来源

##### 2.6.1.1 指令数据

- **数据来源**：指令表、指令匹配记录、指令认领记录
- **权限过滤**：按用户角色和数据权限过滤
- **状态计算**：与指令宝模块保持一致

##### 2.6.1.2 通知数据

- **数据来源**：风控告警记录（被转通知的记录）、系统通知表
- **权限过滤**：只显示当前用户被转发的通知或系统通知
- **状态同步**：与风控告警记录保持状态同步

#### 2.6.2 权限控制

##### 2.6.2.1 一级目录权限

- 一级目录的显示/隐藏由后端根据用户角色动态返回
- 用户只能看到自己有权限访问的一级目录

##### 2.6.2.2 指令工作台权限

- 三级目录的显示/隐藏由后端根据用户角色动态返回
- 指令数据的查看权限按用户角色和数据范围过滤
- 指令操作权限（创建、认领、撤回等）按指令权限设置控制

##### 2.6.2.3 通知列表权限

- 只显示当前用户被转发的通知或系统通知
- 通知处理操作权限按用户角色控制

---

### 2.7 界面状态保存

#### 2.7.1 保存内容

- 用户当前打开的页面（Tab页签状态）
- 页面布局配置（如：列显示/隐藏、列宽、排序、筛选条件等）
- 左侧菜单的折叠/展开状态
- 通知列表的查看位置（用于红点提醒）

#### 2.7.2 保存方式

- 将用户当前页面和布局信息传到期现服务端进行记录
- 按用户ID（hedge_user）和设备类型分别存储

#### 2.7.3 设备类型区分

- PC端和移动端（手机）的界面状态需要分开存储
- 用户在不同设备上登录时，会加载对应设备的界面状态
- 例如：用户在PC上打开的Tab和布局，不会影响手机端的界面状态

#### 2.7.4 异地登录恢复

- 用户在不同地点登录时，系统会加载该用户在该设备类型下保存的界面状态
- 确保用户无论在哪里登录，都能看到上次使用时的界面布局

---

### 2.8 其他补充

#### 2.8.1 角色与权限管理

- 角色定义在全局设置-人员管理-人员角色管理中配置
- 用户角色分配在人员管理中配置
- 指令权限设置在指令宝-指令权限设置中配置

#### 2.8.2 数据一致性

- 指令工作台的数据与指令宝模块保持一致
- 通知列表的数据与风控告警记录保持一致
- 数据更新采用实时或准实时同步

#### 2.8.3 性能优化

- 指令工作台列表支持分页加载
- 通知列表支持分页加载
- 支持数据缓存，减少服务器压力
- 手机端优化加载速度，减少数据量

#### 2.8.4 审计要求

- 所有通知处理操作需留痕
- 包括操作人、时间、处理动作（关注/已处理/转通知）、备注
- 支持按时间/处理人/对象查询历史处理情况

---

## 三、数据的单元与集成测试

### 3.1 测试方法说明

本测试方案采用**数据驱动的接口测试**方法：

1. **准备测试数据**：通过数据库直接插入或调用初始化接口，准备测试所需的基础数据
2. **调用接口**：模拟用户操作，调用相应的API接口
3. **验证输出**：检查接口返回的数据是否符合预期，验证业务逻辑的正确性

所有测试用例都应能够自动化执行，每次代码更新后自动运行，快速发现回归问题。

---

### 3.2 测试数据准备（基线数据）

#### 3.2.1 用户与角色数据

**数据准备SQL或初始化接口调用：**

```sql
-- 插入测试用户
INSERT INTO hedge_user (id, name, phone, status) VALUES
(1001, '测试管理员', '13800001001', 'enable'),
(1002, '测试交易员', '13800001002', 'enable'),
(1003, '测试风控员', '13800001003', 'enable'),
(1004, '测试业务经理', '13800001004', 'enable'),
(1005, '测试制单员', '13800001005', 'enable'),
(1006, '测试指令员', '13800001006', 'enable'),
(1007, '测试客商', '13800001007', 'enable'),
(1008, '测试多角色用户', '13800001008', 'enable');

-- 插入角色
INSERT INTO role (id, name, status) VALUES
(2001, '超级管理员', 'enable'),
(2002, '交易员', 'enable'),
(2003, '风控员', 'enable'),
(2004, '业务经理', 'enable'),
(2005, '制单员', 'enable'),
(2006, '指令员', 'enable'),
(2007, '客商', 'enable');

-- 分配用户角色
INSERT INTO user_role (user_id, role_id) VALUES
(1001, 2001), -- 管理员：超级管理员
(1002, 2002), -- 交易员：交易员
(1003, 2003), -- 风控员：风控员
(1004, 2004), -- 业务经理：业务经理
(1005, 2005), -- 制单员：制单员
(1006, 2006), -- 指令员：指令员
(1007, 2007), -- 客商：客商
(1008, 2002), -- 多角色用户：交易员
(1008, 2004); -- 多角色用户：业务经理
```

#### 3.2.2 指令数据

```sql
-- 插入保值方案
INSERT INTO hedge_plan (id, name, type, status) VALUES
(3001, '测试保值方案-整体匹配', '整体匹配', 'enable'),
(3002, '测试保值方案-单单匹配', '单单匹配', 'enable');

-- 插入指令数据
INSERT INTO instruction (id, hedge_plan_id, creator_id, instruction_type, status, create_time) VALUES
(4001, 3001, 1004, '期货交易指令', '待匹配', '2024-01-15 10:00:00'), -- 业务经理创建的指令
(4002, 3001, 1007, '期货交易指令', '待认领', '2024-01-15 11:00:00'), -- 客商创建的指令
(4003, 3002, 1004, '合同创建指令', '待制单', '2024-01-15 12:00:00'), -- 业务经理创建，待制单
(4004, 3001, 1004, '期货交易指令', '待匹配', '2024-01-15 13:00:00'); -- 业务经理创建的另一条指令
```

#### 3.2.3 告警与通知数据

```sql
-- 插入风控告警记录
INSERT INTO risk_alert (id, alert_no, alert_type, title, content, status, create_time, creator_id) VALUES
(5001, 'ALERT001', '价格异常', '测试告警1', '测试告警内容1', '未处理', '2024-01-15 14:00:00', 1003),
(5002, 'ALERT002', '持仓异常', '测试告警2', '测试告警内容2', '关注中', '2024-01-15 15:00:00', 1003),
(5003, 'ALERT003', '价格异常', '测试告警3', '测试告警内容3', '未处理', '2024-01-15 16:00:00', 1003);

-- 插入告警转通知记录
INSERT INTO alert_to_notification (id, alert_id, target_user_id, notification_no, status, create_time) VALUES
(6001, 5001, 1003, 'NOTIFY001', '未处理', '2024-01-15 14:05:00'), -- 告警1转通知给风控员
(6002, 5001, 1001, 'NOTIFY002', '未处理', '2024-01-15 14:05:00'); -- 告警1转通知给管理员

-- 插入系统通知
INSERT INTO system_notification (id, notification_no, notification_type, title, content, target_user_id, status, create_time) VALUES
(7001, 'SYS001', '系统公告', '测试系统通知', '测试系统通知内容', 1001, '未处理', '2024-01-15 17:00:00');
```

#### 3.2.4 界面状态数据

```sql
-- 插入界面状态记录
INSERT INTO user_interface_state (user_id, device_type, tab_state, layout_config, filter_config, update_time) VALUES
(1001, 'PC', '{"activeTab": "通知列表", "openTabs": ["通知列表", "指令工作台"]}', 
 '{"hiddenColumns": ["备注"], "columnWidths": {"通知时间": 150}}', 
 '{"timeRange": "近7日"}', '2024-01-15 18:00:00'),
(1001, 'MOBILE', '{"activeTab": "交易宝"}', 
 '{}', 
 '{"timeRange": "近7日"}', '2024-01-15 18:00:00');
```

---

### 3.3 接口测试用例

#### 3.3.1 一级目录权限测试

**用例编号**：TEST-001  
**测试模块**：工作台 - 一级目录权限  
**测试场景**：验证不同角色用户登录后，返回的一级目录列表是否符合权限规则

**预置数据**：
- 使用3.2.1节准备的用户和角色数据

**测试步骤**：

1. **调用登录接口，获取token**
   ```
   POST /api/auth/login
   Request Body:
   {
     "username": "测试管理员",
     "password": "password123"
   }
   ```

2. **调用获取一级目录接口**
   ```
   GET /api/workbench/menus/level1
   Headers:
   {
     "Authorization": "Bearer {token}"
   }
   ```

**预期结果**：

| 用户ID | 用户角色 | 预期返回的一级目录 | 验证点 |
|--------|----------|-------------------|--------|
| 1001 | 超级管理员 | 合同宝、指令宝、匹配宝、交易宝、风控宝、报表、全局设置 | 应包含全部7个目录 |
| 1002 | 交易员 | 交易宝 | 仅包含交易宝 |
| 1003 | 风控员 | 风控宝、报表 | 包含风控宝和报表 |
| 1004 | 业务经理 | 合同宝 | 仅包含合同宝 |
| 1005 | 制单员 | 合同宝 | 仅包含合同宝 |
| 1006 | 指令员 | 指令宝 | 仅包含指令宝 |
| 1007 | 客商 | 无 | 空数组（客商不显示一级目录，直接进入工作台） |
| 1008 | 交易员+业务经理 | 交易宝、合同宝 | 两个角色并集 |

**验证代码示例**：
```python
# 预期结果验证
import pytest
import requests

def test_multi_role_user_menus(base_url, auth_token):
    """测试多角色用户的一级目录"""
    headers = {"Authorization": f"Bearer {auth_token}"}
    response = requests.get(f"{base_url}/api/workbench/menus/level1", headers=headers)
    
    assert response.status_code == 200
    data = response.json()
    menus = data.get("menus", [])
    
    assert "交易宝" in menus
    assert "合同宝" in menus
    assert len(menus) == 2
    assert "全局设置" not in menus  # 非管理员不应包含
```

---

#### 3.3.2 指令工作台三级目录测试

**用例编号**：TEST-002  
**测试模块**：工作台 - 指令工作台三级目录  
**测试场景**：验证不同角色用户看到的指令工作台三级目录是否正确

**预置数据**：
- 使用3.2.1节准备的用户和角色数据

**测试步骤**：

1. **调用登录接口，获取token**（以业务经理为例）
   ```
   POST /api/auth/login
   Request Body:
   {
     "username": "测试业务经理",
     "password": "password123"
   }
   ```

2. **调用获取指令工作台三级目录接口**
   ```
   GET /api/workbench/instruction-workspace/level3-menus
   Headers:
   {
     "Authorization": "Bearer {token}"
   }
   ```

**预期结果**：

| 用户ID | 用户角色 | 预期返回的三级目录 | 验证点 |
|--------|----------|-------------------|--------|
| 1004 | 业务经理 | 业务经理工作台 | 仅包含业务经理工作台 |
| 1002 | 交易员 | 交易员工作台 | 仅包含交易员工作台 |
| 1006 | 指令员 | 指令员工作台 | 仅包含指令员工作台 |
| 1005 | 制单员 | 制单员工作台 | 仅包含制单员工作台 |
| 1007 | 客商 | 客商工作台 | 仅包含客商工作台 |
| 1008 | 交易员+业务经理 | 交易员工作台、业务经理工作台 | 两个角色并集 |

**验证代码示例**：
```python
def test_multi_role_level3_menus(base_url, auth_token):
    """测试多角色用户的三级目录"""
    headers = {"Authorization": f"Bearer {auth_token}"}
    response = requests.get(
        f"{base_url}/api/workbench/instruction-workspace/level3-menus",
        headers=headers
    )
    
    assert response.status_code == 200
    data = response.json()
    menus = data.get("menus", [])
    
    assert len(menus) == 2
    menu_ids = [menu["id"] for menu in menus]
    assert "trader-workspace" in menu_ids
    assert "bm-workspace" in menu_ids
    
    menu_names = [menu["name"] for menu in menus]
    assert "交易员工作台" in menu_names
    assert "业务经理工作台" in menu_names
```

---

#### 3.3.3 指令列表权限过滤测试

**用例编号**：TEST-003  
**测试模块**：工作台 - 指令工作台指令列表  
**测试场景**：验证不同角色用户看到的指令列表是否正确过滤

**预置数据**：
- 使用3.2.1和3.2.2节准备的用户、角色和指令数据

**测试步骤**：

1. **业务经理登录并查询指令列表**
   ```
   POST /api/auth/login
   GET /api/workbench/instruction-workspace/bm-workspace/instructions
   Query Parameters:
   {
     "page": 1,
     "pageSize": 20
   }
   ```

2. **客商登录并查询指令列表**
   ```
   POST /api/auth/login (使用客商账号)
   GET /api/workbench/instruction-workspace/customer-workspace/instructions
   ```

3. **交易员登录并查询指令列表**
   ```
   POST /api/auth/login (使用交易员账号)
   GET /api/workbench/instruction-workspace/trader-workspace/instructions
   ```

4. **制单员登录并查询指令列表**
   ```
   POST /api/auth/login (使用制单员账号)
   GET /api/workbench/instruction-workspace/doc-workspace/instructions
   ```

**预期结果**：

| 用户角色 | 用户ID | 应看到的指令 | 不应看到的指令 | 验证点 |
|----------|--------|-------------|---------------|--------|
| 业务经理 | 1004 | 4001, 4003, 4004（自己创建的） | 4002（客商创建的） | 只看自己发起的指令 |
| 客商 | 1007 | 4002（自己创建的） | 4001, 4003, 4004 | 只看自己发起的指令 |
| 交易员 | 1002 | 4001, 4002, 4003, 4004（所有交易指令） | 无 | 可查看所有交易指令 |
| 制单员 | 1005 | 4003（状态为待制单的） | 4001, 4002, 4004 | 只看待制单的指令 |

**验证代码示例**：
```python
def test_business_manager_instructions(base_url, bm_auth_token):
    """测试业务经理的指令列表权限"""
    headers = {"Authorization": f"Bearer {bm_auth_token}"}
    params = {"page": 1, "pageSize": 20}
    response = requests.get(
        f"{base_url}/api/workbench/instruction-workspace/bm-workspace/instructions",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    data = response.json()
    instructions = data.get("list", [])
    
    assert len(instructions) == 3
    instruction_ids = [inst["id"] for inst in instructions]
    assert 4001 in instruction_ids
    assert 4003 in instruction_ids
    assert 4004 in instruction_ids
    assert 4002 not in instruction_ids  # 不应包含客商的指令

def test_doc_maker_instructions(base_url, doc_auth_token):
    """测试制单员的指令列表权限"""
    headers = {"Authorization": f"Bearer {doc_auth_token}"}
    params = {"page": 1, "pageSize": 20}
    response = requests.get(
        f"{base_url}/api/workbench/instruction-workspace/doc-workspace/instructions",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    data = response.json()
    instructions = data.get("list", [])
    
    assert len(instructions) == 1
    assert instructions[0]["id"] == 4003
    assert instructions[0]["status"] == "待制单"
```

---

#### 3.3.4 通知列表查询测试

**用例编号**：TEST-004  
**测试模块**：工作台 - 通知列表  
**测试场景**：验证通知列表查询和权限过滤

**预置数据**：
- 使用3.2.3节准备的告警和通知数据

**测试步骤**：

1. **风控员登录并查询通知列表**
   ```
   POST /api/auth/login (用户ID: 1003)
   GET /api/workbench/notifications
   Query Parameters:
   {
     "page": 1,
     "pageSize": 20,
     "notificationType": "告警转通知",
     "status": "未处理"
   }
   ```

2. **管理员登录并查询通知列表**
   ```
   POST /api/auth/login (用户ID: 1001)
   GET /api/workbench/notifications
   ```

**预期结果**：

| 用户ID | 应看到的通知 | 验证点 |
|--------|-------------|--------|
| 1003（风控员） | NOTIFY001（告警1转通知） | 只看到转给自己的通知 |
| 1001（管理员） | NOTIFY002（告警1转通知）、SYS001（系统通知） | 看到转给自己的告警转通知和系统通知 |

**验证代码示例**：
```python
def test_risk_manager_notifications(base_url, rm_auth_token):
    """测试风控员的通知列表"""
    headers = {"Authorization": f"Bearer {rm_auth_token}"}
    params = {
        "page": 1,
        "pageSize": 20,
        "notificationType": "告警转通知",
        "status": "未处理"
    }
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    data = response.json()
    notifications = data.get("list", [])
    
    assert len(notifications) == 1
    assert notifications[0]["notificationNo"] == "NOTIFY001"
    assert notifications[0]["source"] == "风控告警"
    assert notifications[0]["status"] == "未处理"

def test_admin_notifications(base_url, admin_auth_token):
    """测试管理员的通知列表"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    params = {"page": 1, "pageSize": 20}
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    data = response.json()
    notifications = data.get("list", [])
    
    assert len(notifications) >= 2
    notification_nos = [n["notificationNo"] for n in notifications]
    assert "NOTIFY002" in notification_nos
    assert "SYS001" in notification_nos
```

---

#### 3.3.5 通知处理操作测试

**用例编号**：TEST-005  
**测试模块**：工作台 - 通知列表处理操作  
**测试场景**：验证通知的"关注"、"已处理"、"转通知"操作及状态同步

**预置数据**：
- 使用3.2.3节准备的告警和通知数据

**测试步骤**：

1. **关注操作**
   ```
   POST /api/auth/login (用户ID: 1003)
   POST /api/workbench/notifications/{notificationId}/follow
   Request Body:
   {
     "remark": "需要持续关注"
   }
   ```

2. **验证通知状态和告警备注**
   ```
   GET /api/workbench/notifications/{notificationId}
   GET /api/risk/alerts/{alertId}
   ```

3. **已处理操作**
   ```
   POST /api/workbench/notifications/{notificationId}/mark-processed
   Request Body:
   {
     "remark": "已处理完成"
   }
   ```

4. **转通知操作**
   ```
   POST /api/workbench/notifications/{notificationId}/forward
   Request Body:
   {
     "targetUserIds": [1001, 1004],
     "remark": "转发给管理员和业务经理"
   }
   ```

**预期结果**：

| 操作 | 通知状态变化 | 告警备注变化 | 验证点 |
|------|-------------|-------------|--------|
| 关注 | 未处理 → 关注中 | 备注中添加"测试风控员 关注 需要持续关注 [2024-01-15 14:10:00]" | 状态同步，备注记录操作 |
| 已处理 | 关注中 → 已处理 | 备注中添加"测试风控员 已处理 已处理完成 [2024-01-15 14:15:00]" | 状态同步，备注记录操作 |
| 转通知 | 不变 | 备注中添加"测试风控员 转通知 测试管理员,测试业务经理 转发给管理员和业务经理 [2024-01-15 14:20:00]" | 备注记录操作，目标用户收到通知 |

**验证代码示例**：
```python
def test_notification_follow(base_url, rm_auth_token, notification_id, alert_id):
    """测试通知关注操作"""
    headers = {"Authorization": f"Bearer {rm_auth_token}"}
    data = {"remark": "需要持续关注"}
    
    # 执行关注操作
    response = requests.post(
        f"{base_url}/api/workbench/notifications/{notification_id}/follow",
        headers=headers,
        json=data
    )
    assert response.status_code == 200
    
    # 验证通知状态
    get_notification_resp = requests.get(
        f"{base_url}/api/workbench/notifications/{notification_id}",
        headers=headers
    )
    notification = get_notification_resp.json().get("data", {})
    assert notification["status"] == "关注中"
    
    # 验证告警备注
    get_alert_resp = requests.get(
        f"{base_url}/api/risk/alerts/{alert_id}",
        headers=headers
    )
    alert = get_alert_resp.json().get("data", {})
    assert "测试风控员 关注" in alert.get("remark", "")

def test_notification_mark_processed(base_url, rm_auth_token, notification_id, alert_id):
    """测试通知已处理操作"""
    headers = {"Authorization": f"Bearer {rm_auth_token}"}
    data = {"remark": "已处理完成"}
    
    response = requests.post(
        f"{base_url}/api/workbench/notifications/{notification_id}/mark-processed",
        headers=headers,
        json=data
    )
    assert response.status_code == 200
    
    # 验证通知状态
    get_notification_resp = requests.get(
        f"{base_url}/api/workbench/notifications/{notification_id}",
        headers=headers
    )
    notification = get_notification_resp.json().get("data", {})
    assert notification["status"] == "已处理"
    assert notification["processorId"] == 1003
    
    # 验证告警备注
    get_alert_resp = requests.get(
        f"{base_url}/api/risk/alerts/{alert_id}",
        headers=headers
    )
    alert = get_alert_resp.json().get("data", {})
    assert "测试风控员 已处理" in alert.get("remark", "")

def test_notification_forward(base_url, rm_auth_token, notification_id, alert_id):
    """测试通知转通知操作"""
    headers = {"Authorization": f"Bearer {rm_auth_token}"}
    data = {
        "targetUserIds": [1001, 1004],
        "remark": "转发给管理员和业务经理"
    }
    
    response = requests.post(
        f"{base_url}/api/workbench/notifications/{notification_id}/forward",
        headers=headers,
        json=data
    )
    assert response.status_code == 200
    
    # 验证目标用户收到通知
    admin_headers = {"Authorization": f"Bearer {admin_auth_token}"}
    admin_resp = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=admin_headers,
        params={"notificationIds": notification_id}
    )
    admin_notifications = admin_resp.json().get("data", {}).get("list", [])
    assert len(admin_notifications) > 0
    assert any(n.get("sourceAlertId") == alert_id for n in admin_notifications)
```

---

#### 3.3.6 红点提醒测试

**用例编号**：TEST-006  
**测试模块**：工作台 - 通知列表红点提醒  
**测试场景**：验证红点提醒的逻辑和状态记录

**预置数据**：
- 使用3.2.3节准备的告警和通知数据
- 清空用户的查看记录

**测试步骤**：

1. **查询通知列表，获取查看位置**
   ```
   POST /api/auth/login (用户ID: 1003)
   GET /api/workbench/notifications
   Query Parameters:
   {
     "page": 1,
     "pageSize": 10
   }
   ```

2. **记录查看位置**
   ```
   POST /api/workbench/notifications/mark-viewed
   Request Body:
   {
     "lastViewedNotificationId": "NOTIFY001",
     "viewTime": "2024-01-15 14:30:00"
   }
   ```

3. **新增通知后查询红点状态**
   ```
   -- 先插入新通知（模拟系统生成新通知）
   INSERT INTO alert_to_notification (alert_id, target_user_id, notification_no, status, create_time)
   VALUES (5003, 1003, 'NOTIFY003', '未处理', '2024-01-15 14:35:00');
   
   -- 查询红点状态
   GET /api/workbench/notifications/badge-status
   ```

4. **再次查询通知列表**
   ```
   GET /api/workbench/notifications
   ```

**预期结果**：

| 步骤 | 预期结果 | 验证点 |
|------|---------|--------|
| 1. 首次查询 | 返回通知列表，所有通知标记为"new" | 新通知应显示new标记 |
| 2. 记录查看位置 | 返回成功，记录保存到数据库 | 查看位置应记录到user_notification_view_record表 |
| 3. 查询红点状态 | 返回{"hasNew": true, "badgeCount": 1} | 有新通知应显示红点 |
| 4. 再次查询 | NOTIFY003标记为"new"，NOTIFY001不标记 | 只有上次查看后的新通知显示new |

**验证代码示例**：
```python
def test_badge_reminder(base_url, rm_auth_token):
    """测试红点提醒功能"""
    headers = {"Authorization": f"Bearer {rm_auth_token}"}
    
    # 1. 首次查询验证
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params={"page": 1, "pageSize": 10}
    )
    assert response.status_code == 200
    notifications = response.json().get("data", {}).get("list", [])
    assert all(n.get("isNew") for n in notifications)
    
    # 2. 记录查看位置
    mark_viewed_data = {
        "lastViewedNotificationId": "NOTIFY001",
        "viewTime": "2024-01-15 14:30:00"
    }
    mark_viewed_resp = requests.post(
        f"{base_url}/api/workbench/notifications/mark-viewed",
        headers=headers,
        json=mark_viewed_data
    )
    assert mark_viewed_resp.status_code == 200
    
    # 3. 查询红点状态（假设已新增通知NOTIFY003）
    badge_resp = requests.get(
        f"{base_url}/api/workbench/notifications/badge-status",
        headers=headers
    )
    badge_data = badge_resp.json().get("data", {})
    assert badge_data.get("hasNew") is True
    assert badge_data.get("badgeCount") >= 1
    
    # 4. 再次查询验证
    list_resp = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers
    )
    notifications2 = list_resp.json().get("data", {}).get("list", [])
    notify003 = next((n for n in notifications2 if n.get("notificationNo") == "NOTIFY003"), None)
    notify001 = next((n for n in notifications2 if n.get("notificationNo") == "NOTIFY001"), None)
    
    if notify003:
        assert notify003.get("isNew") is True
    if notify001:
        assert notify001.get("isNew") is False
```

---

#### 3.3.7 界面状态保存和恢复测试

**用例编号**：TEST-007  
**测试模块**：工作台 - 界面状态保存  
**测试场景**：验证PC端和移动端的界面状态分别保存和恢复

**预置数据**：
- 清空用户的界面状态记录

**测试步骤**：

1. **PC端保存界面状态**
   ```
   POST /api/auth/login (用户ID: 1001)
   POST /api/workbench/interface-state/save
   Request Body:
   {
     "deviceType": "PC",
     "tabState": {
       "activeTab": "通知列表",
       "openTabs": ["通知列表", "指令工作台"]
     },
     "layoutConfig": {
       "hiddenColumns": ["备注"],
       "columnWidths": {
         "通知时间": 150,
         "通知标题": 200
       }
     },
     "filterConfig": {
       "timeRange": "近7日",
       "notificationType": ["告警转通知"]
     }
   }
   ```

2. **移动端保存界面状态**
   ```
   POST /api/workbench/interface-state/save
   Request Body:
   {
     "deviceType": "MOBILE",
     "tabState": {
       "activeTab": "交易宝"
     },
     "layoutConfig": {},
     "filterConfig": {
       "timeRange": "近7日"
     }
   }
   ```

3. **PC端恢复界面状态**
   ```
   GET /api/workbench/interface-state/restore
   Query Parameters:
   {
     "deviceType": "PC"
   }
   ```

4. **移动端恢复界面状态**
   ```
   GET /api/workbench/interface-state/restore
   Query Parameters:
   {
     "deviceType": "MOBILE"
   }
   ```

**预期结果**：

| 步骤 | 预期结果 | 验证点 |
|------|---------|--------|
| 1. PC端保存 | 返回成功，数据保存到数据库 | 状态应保存到user_interface_state表，deviceType=PC |
| 2. 移动端保存 | 返回成功，数据保存到数据库 | 状态应保存到user_interface_state表，deviceType=MOBILE |
| 3. PC端恢复 | 返回PC端保存的状态 | 应返回步骤1保存的PC端状态 |
| 4. 移动端恢复 | 返回移动端保存的状态 | 应返回步骤2保存的移动端状态 |

**验证代码示例**：
```python
def test_interface_state_save_and_restore(base_url, admin_auth_token):
    """测试界面状态保存和恢复"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    
    # 1. PC端保存界面状态
    pc_state_data = {
        "deviceType": "PC",
        "tabState": {
            "activeTab": "通知列表",
            "openTabs": ["通知列表", "指令工作台"]
        },
        "layoutConfig": {
            "hiddenColumns": ["备注"],
            "columnWidths": {
                "通知时间": 150,
                "通知标题": 200
            }
        },
        "filterConfig": {
            "timeRange": "近7日",
            "notificationType": ["告警转通知"]
        }
    }
    save_pc_resp = requests.post(
        f"{base_url}/api/workbench/interface-state/save",
        headers=headers,
        json=pc_state_data
    )
    assert save_pc_resp.status_code == 200
    
    # 2. 移动端保存界面状态
    mobile_state_data = {
        "deviceType": "MOBILE",
        "tabState": {
            "activeTab": "交易宝"
        },
        "layoutConfig": {},
        "filterConfig": {
            "timeRange": "近7日"
        }
    }
    save_mobile_resp = requests.post(
        f"{base_url}/api/workbench/interface-state/save",
        headers=headers,
        json=mobile_state_data
    )
    assert save_mobile_resp.status_code == 200
    
    # 3. PC端恢复验证
    restore_pc_resp = requests.get(
        f"{base_url}/api/workbench/interface-state/restore",
        headers=headers,
        params={"deviceType": "PC"}
    )
    assert restore_pc_resp.status_code == 200
    pc_restored = restore_pc_resp.json().get("data", {})
    assert pc_restored.get("deviceType") == "PC"
    assert pc_restored.get("tabState", {}).get("activeTab") == "通知列表"
    assert "备注" in pc_restored.get("layoutConfig", {}).get("hiddenColumns", [])
    
    # 4. 移动端恢复验证
    restore_mobile_resp = requests.get(
        f"{base_url}/api/workbench/interface-state/restore",
        headers=headers,
        params={"deviceType": "MOBILE"}
    )
    assert restore_mobile_resp.status_code == 200
    mobile_restored = restore_mobile_resp.json().get("data", {})
    assert mobile_restored.get("deviceType") == "MOBILE"
    assert mobile_restored.get("tabState", {}).get("activeTab") == "交易宝"
```

---

#### 3.3.8 手机分辨率适配测试

**用例编号**：TEST-008  
**测试模块**：工作台 - 手机分辨率适配  
**测试场景**：验证不同分辨率下返回的菜单结构是否正确

**预置数据**：
- 使用3.2.1节准备的用户数据
- 假设分辨率阈值为768px（参考0_系统缺省默认.md）

**测试步骤**：

1. **PC分辨率登录（≥768px）**
   ```
   POST /api/auth/login
   Request Headers:
   {
     "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
     "Screen-Width": "1920",
     "Screen-Height": "1080"
   }
   GET /api/workbench/menus/level1
   ```

2. **手机分辨率登录（<768px）**
   ```
   POST /api/auth/login
   Request Headers:
   {
     "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X)",
     "Screen-Width": "375",
     "Screen-Height": "667"
   }
   GET /api/workbench/menus/level1
   ```

**预期结果**：

| 分辨率 | 设备类型 | 一级目录 | 验证点 |
|--------|---------|---------|--------|
| 1920x1080 | PC | 根据角色返回完整目录（如：合同宝、指令宝、匹配宝、交易宝、风控宝、报表、全局设置） | PC端显示所有有权限的目录 |
| 375x667 | MOBILE | 工作台、交易宝 | 手机端仅显示工作台和交易宝 |
| 767x1024 | MOBILE | 工作台、交易宝 | 边界值，小于768视为移动端 |
| 768x1024 | PC | 根据角色返回完整目录 | 边界值，大于等于768视为PC端 |

**验证代码示例**：
```python
def test_pc_resolution_menus(base_url, admin_auth_token):
    """测试PC分辨率的菜单"""
    headers = {
        "Authorization": f"Bearer {admin_auth_token}",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
        "Screen-Width": "1920",
        "Screen-Height": "1080"
    }
    response = requests.get(
        f"{base_url}/api/workbench/menus/level1",
        headers=headers
    )
    
    assert response.status_code == 200
    data = response.json().get("data", {})
    assert data.get("deviceType") == "PC"
    menus = data.get("menus", [])
    assert len(menus) > 2
    assert "全局设置" in menus  # PC端应有全局设置

def test_mobile_resolution_menus(base_url, admin_auth_token):
    """测试手机分辨率的菜单"""
    headers = {
        "Authorization": f"Bearer {admin_auth_token}",
        "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X)",
        "Screen-Width": "375",
        "Screen-Height": "667"
    }
    response = requests.get(
        f"{base_url}/api/workbench/menus/level1",
        headers=headers
    )
    
    assert response.status_code == 200
    data = response.json().get("data", {})
    assert data.get("deviceType") == "MOBILE"
    menus = data.get("menus", [])
    assert len(menus) == 2
    assert "工作台" in menus
    assert "交易宝" in menus
    assert "全局设置" not in menus  # 移动端不应有全局设置
```

---

#### 3.3.9 通知列表分页和筛选测试

**用例编号**：TEST-009  
**测试模块**：工作台 - 通知列表分页筛选  
**测试场景**：验证通知列表的分页、排序和筛选功能

**预置数据**：
- 准备20条通知记录（时间分布在最近30天内）

**测试步骤**：

1. **分页查询**
   ```
   GET /api/workbench/notifications
   Query Parameters:
   {
     "page": 1,
     "pageSize": 10
   }
   ```

2. **时间范围筛选**
   ```
   GET /api/workbench/notifications
   Query Parameters:
   {
     "timeRange": "近7日",
     "startDate": "2024-01-08",
     "endDate": "2024-01-15"
   }
   ```

3. **多条件筛选**
   ```
   GET /api/workbench/notifications
   Query Parameters:
   {
     "notificationType": ["告警转通知", "系统通知"],
     "status": "未处理",
     "source": "风控告警"
   }
   ```

4. **排序测试**
   ```
   GET /api/workbench/notifications
   Query Parameters:
   {
     "sortField": "createTime",
     "sortOrder": "DESC"
   }
   ```

**预期结果**：

| 测试项 | 预期结果 | 验证点 |
|--------|---------|--------|
| 分页查询 | 第1页返回10条，total=20 | 分页参数生效，total正确 |
| 时间范围筛选 | 只返回指定时间范围内的通知 | 筛选条件生效 |
| 多条件筛选 | 返回同时满足所有条件的通知 | 多条件AND关系 |
| 排序 | 按创建时间降序排列 | 排序字段和顺序正确 |

**验证代码示例**：
```python
from datetime import datetime, timedelta

def test_notifications_pagination(base_url, admin_auth_token):
    """测试通知列表分页"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    params = {"page": 1, "pageSize": 10}
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    data = response.json().get("data", {})
    assert len(data.get("list", [])) == 10
    assert data.get("total") == 20
    assert data.get("page") == 1
    assert data.get("pageSize") == 10

def test_notifications_time_range_filter(base_url, admin_auth_token):
    """测试通知列表时间范围筛选"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    params = {"timeRange": "近7日"}
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    notifications = response.json().get("data", {}).get("list", [])
    seven_days_ago = datetime.now() - timedelta(days=7)
    
    for item in notifications:
        create_time = datetime.fromisoformat(item.get("createTime").replace("Z", "+00:00"))
        assert create_time >= seven_days_ago

def test_notifications_multi_filter(base_url, admin_auth_token):
    """测试通知列表多条件筛选"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    params = {
        "notificationType": ["告警转通知"],
        "status": "未处理"
    }
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    notifications = response.json().get("data", {}).get("list", [])
    
    for item in notifications:
        assert item.get("notificationType") == "告警转通知"
        assert item.get("status") == "未处理"

def test_notifications_sort(base_url, admin_auth_token):
    """测试通知列表排序"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    params = {
        "sortField": "createTime",
        "sortOrder": "DESC"
    }
    response = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=params
    )
    
    assert response.status_code == 200
    notifications = response.json().get("data", {}).get("list", [])
    
    for i in range(len(notifications) - 1):
        current = datetime.fromisoformat(notifications[i].get("createTime").replace("Z", "+00:00"))
        next_item = datetime.fromisoformat(notifications[i + 1].get("createTime").replace("Z", "+00:00"))
        assert current >= next_item
```

---

#### 3.3.10 批量处理通知测试

**用例编号**：TEST-010  
**测试模块**：工作台 - 通知列表批量处理  
**测试场景**：验证批量处理通知的功能

**预置数据**：
- 准备5条未处理的通知记录

**测试步骤**：

1. **批量关注**
   ```
   POST /api/workbench/notifications/batch-follow
   Request Body:
   {
     "notificationIds": [6001, 6002, 6003],
     "remark": "批量标记关注"
   }
   ```

2. **批量已处理**
   ```
   POST /api/workbench/notifications/batch-mark-processed
   Request Body:
   {
     "notificationIds": [6004, 6005],
     "remark": "批量标记已处理"
   }
   ```

3. **验证处理结果**
   ```
   GET /api/workbench/notifications
   Query Parameters:
   {
     "notificationIds": "6001,6002,6003,6004,6005"
   }
   ```

**预期结果**：

| 操作 | 通知ID | 状态变化 | 验证点 |
|------|--------|---------|--------|
| 批量关注 | 6001, 6002, 6003 | 未处理 → 关注中 | 3条通知状态变为关注中 |
| 批量已处理 | 6004, 6005 | 未处理 → 已处理 | 2条通知状态变为已处理 |

**验证代码示例**：
```python
def test_batch_notification_operations(base_url, admin_auth_token):
    """测试批量通知处理"""
    headers = {"Authorization": f"Bearer {admin_auth_token}"}
    
    # 1. 批量关注
    batch_follow_data = {
        "notificationIds": [6001, 6002, 6003],
        "remark": "批量标记关注"
    }
    batch_follow_resp = requests.post(
        f"{base_url}/api/workbench/notifications/batch-follow",
        headers=headers,
        json=batch_follow_data
    )
    assert batch_follow_resp.status_code == 200
    assert batch_follow_resp.json().get("data", {}).get("successCount") == 3
    
    # 2. 批量已处理
    batch_processed_data = {
        "notificationIds": [6004, 6005],
        "remark": "批量标记已处理"
    }
    batch_processed_resp = requests.post(
        f"{base_url}/api/workbench/notifications/batch-mark-processed",
        headers=headers,
        json=batch_processed_data
    )
    assert batch_processed_resp.status_code == 200
    assert batch_processed_resp.json().get("data", {}).get("successCount") == 2
    
    # 3. 验证处理结果
    verify_params = {"notificationIds": "6001,6002,6003,6004,6005"}
    verify_resp = requests.get(
        f"{base_url}/api/workbench/notifications",
        headers=headers,
        params=verify_params
    )
    notifications = verify_resp.json().get("data", {}).get("list", [])
    
    followed = [n for n in notifications if n.get("id") in [6001, 6002, 6003]]
    processed = [n for n in notifications if n.get("id") in [6004, 6005]]
    
    assert all(n.get("status") == "关注中" for n in followed)
    assert all(n.get("status") == "已处理" for n in processed)
```

---

### 3.4 测试数据准备和清理

#### 3.4.1 测试数据准备脚本（scripts/prepare_test_db.py）

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""测试数据库准备脚本"""

import psycopg2
import os
from typing import Dict

DB_CONFIG = {
    "host": os.getenv("DB_HOST", "localhost"),
    "port": int(os.getenv("DB_PORT", "5432")),
    "database": os.getenv("DB_NAME", "test_db"),
    "user": os.getenv("DB_USER", "test_user"),
    "password": os.getenv("DB_PASSWORD", "test_password")
}


def cleanup_test_data(conn):
    """清理测试数据"""
    cleanup_sql = """
    DELETE FROM user_notification_view_record WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM user_interface_state WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM alert_to_notification WHERE id >= 6001;
    DELETE FROM system_notification WHERE id >= 7001;
    DELETE FROM risk_alert WHERE id >= 5001;
    DELETE FROM instruction WHERE id >= 4001;
    DELETE FROM hedge_plan WHERE id >= 3001;
    DELETE FROM user_role WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM role WHERE id >= 2001;
    DELETE FROM hedge_user WHERE id >= 1001 AND id <= 1008;
    """
    with conn.cursor() as cursor:
        cursor.execute(cleanup_sql)
    conn.commit()
    print("✓ 测试数据已清理")


def prepare_test_data(conn):
    """准备测试数据"""
    sql_scripts = [
        # 插入测试用户
        """
        INSERT INTO hedge_user (id, name, phone, status) VALUES
        (1001, '测试管理员', '13800001001', 'enable'),
        (1002, '测试交易员', '13800001002', 'enable'),
        (1003, '测试风控员', '13800001003', 'enable'),
        (1004, '测试业务经理', '13800001004', 'enable'),
        (1005, '测试制单员', '13800001005', 'enable'),
        (1006, '测试指令员', '13800001006', 'enable'),
        (1007, '测试客商', '13800001007', 'enable'),
        (1008, '测试多角色用户', '13800001008', 'enable')
        ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;
        """,
        # 插入角色
        """
        INSERT INTO role (id, name, status) VALUES
        (2001, '超级管理员', 'enable'),
        (2002, '交易员', 'enable'),
        (2003, '风控员', 'enable'),
        (2004, '业务经理', 'enable'),
        (2005, '制单员', 'enable'),
        (2006, '指令员', 'enable'),
        (2007, '客商', 'enable')
        ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;
        """,
        # 分配用户角色
        """
        INSERT INTO user_role (user_id, role_id) VALUES
        (1001, 2001), (1002, 2002), (1003, 2003), (1004, 2004),
        (1005, 2005), (1006, 2006), (1007, 2007),
        (1008, 2002), (1008, 2004)
        ON CONFLICT DO NOTHING;
        """,
        # 插入保值方案
        """
        INSERT INTO hedge_plan (id, name, type, status) VALUES
        (3001, '测试保值方案-整体匹配', '整体匹配', 'enable'),
        (3002, '测试保值方案-单单匹配', '单单匹配', 'enable')
        ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;
        """,
        # 插入指令数据
        """
        INSERT INTO instruction (id, hedge_plan_id, creator_id, instruction_type, status, create_time) VALUES
        (4001, 3001, 1004, '期货交易指令', '待匹配', '2024-01-15 10:00:00'),
        (4002, 3001, 1007, '期货交易指令', '待认领', '2024-01-15 11:00:00'),
        (4003, 3002, 1004, '合同创建指令', '待制单', '2024-01-15 12:00:00'),
        (4004, 3001, 1004, '期货交易指令', '待匹配', '2024-01-15 13:00:00')
        ON CONFLICT (id) DO UPDATE SET status = EXCLUDED.status;
        """,
        # 插入风控告警记录
        """
        INSERT INTO risk_alert (id, alert_no, alert_type, title, content, status, create_time, creator_id) VALUES
        (5001, 'ALERT001', '价格异常', '测试告警1', '测试告警内容1', '未处理', '2024-01-15 14:00:00', 1003),
        (5002, 'ALERT002', '持仓异常', '测试告警2', '测试告警内容2', '关注中', '2024-01-15 15:00:00', 1003),
        (5003, 'ALERT003', '价格异常', '测试告警3', '测试告警内容3', '未处理', '2024-01-15 16:00:00', 1003)
        ON CONFLICT (id) DO UPDATE SET status = EXCLUDED.status;
        """,
        # 插入告警转通知记录
        """
        INSERT INTO alert_to_notification (id, alert_id, target_user_id, notification_no, status, create_time) VALUES
        (6001, 5001, 1003, 'NOTIFY001', '未处理', '2024-01-15 14:05:00'),
        (6002, 5001, 1001, 'NOTIFY002', '未处理', '2024-01-15 14:05:00')
        ON CONFLICT (id) DO UPDATE SET status = EXCLUDED.status;
        """,
        # 插入系统通知
        """
        INSERT INTO system_notification (id, notification_no, notification_type, title, content, target_user_id, status, create_time) VALUES
        (7001, 'SYS001', '系统公告', '测试系统通知', '测试系统通知内容', 1001, '未处理', '2024-01-15 17:00:00')
        ON CONFLICT (id) DO UPDATE SET status = EXCLUDED.status;
        """
    ]
    
    with conn.cursor() as cursor:
        for sql in sql_scripts:
            cursor.execute(sql)
    conn.commit()
    print("✓ 测试数据已准备完成")


def main():
    """主函数"""
    print("开始准备测试数据库...")
    
    try:
        conn = psycopg2.connect(**DB_CONFIG)
        print(f"✓ 数据库连接成功: {DB_CONFIG['database']}")
        
        # 清理旧数据
        cleanup_test_data(conn)
        
        # 准备新数据
        prepare_test_data(conn)
        
        conn.close()
        print("✓ 测试数据库准备完成")
        
    except Exception as e:
        print(f"✗ 错误: {e}")
        raise


if __name__ == "__main__":
    main()
```

#### 3.4.2 测试数据清理

每次测试运行前和运行后，需要清理测试数据，确保测试环境的干净：

```sql
-- 清理测试数据（按依赖关系逆序删除）
DELETE FROM user_notification_view_record WHERE user_id IN (1001, 1002, 1003, 1004, 1005, 1006, 1007, 1008);
DELETE FROM user_interface_state WHERE user_id IN (1001, 1002, 1003, 1004, 1005, 1006, 1007, 1008);
DELETE FROM alert_to_notification WHERE id >= 6001;
DELETE FROM system_notification WHERE id >= 7001;
DELETE FROM risk_alert WHERE id >= 5001;
DELETE FROM instruction WHERE id >= 4001;
DELETE FROM hedge_plan WHERE id >= 3001;
DELETE FROM user_role WHERE user_id IN (1001, 1002, 1003, 1004, 1005, 1006, 1007, 1008);
DELETE FROM role WHERE id >= 2001;
DELETE FROM hedge_user WHERE id >= 1001 AND id <= 1008;
```

---

#### 3.5.1 测试工具类（tests/utils/test_helpers.py）

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""测试工具类"""

import requests
from typing import Dict, Optional, List
import os

BASE_URL = os.getenv("API_BASE_URL", "http://localhost:8000")


class APIClient:
    """API客户端封装类"""
    
    def __init__(self, base_url: str = BASE_URL, token: Optional[str] = None):
        self.base_url = base_url
        self.token = token
        self.session = requests.Session()
        if token:
            self.session.headers.update({"Authorization": f"Bearer {token}"})
    
    def login(self, username: str, password: str) -> Dict:
        """登录接口"""
        response = self.session.post(
            f"{self.base_url}/api/auth/login",
            json={"username": username, "password": password}
        )
        response.raise_for_status()
        data = response.json()
        if data.get("code") == 200:
            self.token = data.get("data", {}).get("token")
            self.session.headers.update({"Authorization": f"Bearer {self.token}"})
        return data
    
    def get(self, endpoint: str, params: Optional[Dict] = None, **kwargs) -> requests.Response:
        """GET请求"""
        return self.session.get(f"{self.base_url}{endpoint}", params=params, **kwargs)
    
    def post(self, endpoint: str, json: Optional[Dict] = None, **kwargs) -> requests.Response:
        """POST请求"""
        return self.session.post(f"{self.base_url}{endpoint}", json=json, **kwargs)
    
    def put(self, endpoint: str, json: Optional[Dict] = None, **kwargs) -> requests.Response:
        """PUT请求"""
        return self.session.put(f"{self.base_url}{endpoint}", json=json, **kwargs)
    
    def delete(self, endpoint: str, **kwargs) -> requests.Response:
        """DELETE请求"""
        return self.session.delete(f"{self.base_url}{endpoint}", **kwargs)


class TestDataBuilder:
    """测试数据构建器"""
    
    @staticmethod
    def build_user_data(user_id: int, name: str, phone: str) -> Dict:
        """构建用户数据"""
        return {
            "id": user_id,
            "name": name,
            "phone": phone,
            "status": "enable"
        }
    
    @staticmethod
    def build_instruction_data(
        instruction_id: int,
        hedge_plan_id: int,
        creator_id: int,
        instruction_type: str,
        status: str
    ) -> Dict:
        """构建指令数据"""
        return {
            "id": instruction_id,
            "hedge_plan_id": hedge_plan_id,
            "creator_id": creator_id,
            "instruction_type": instruction_type,
            "status": status,
            "create_time": "2024-01-15 10:00:00"
        }


def assert_response_success(response: requests.Response, expected_status: int = 200):
    """断言响应成功"""
    assert response.status_code == expected_status, f"Expected {expected_status}, got {response.status_code}"
    if response.headers.get("Content-Type", "").startswith("application/json"):
        data = response.json()
        assert data.get("code") == 200, f"API returned error: {data.get('message')}"
    return response


def assert_list_length(response: requests.Response, expected_length: int, field: str = "list"):
    """断言列表长度"""
    data = response.json().get("data", {})
    actual_list = data.get(field, [])
    assert len(actual_list) == expected_length, \
        f"Expected {expected_length} items, got {len(actual_list)}"
    return actual_list


def assert_in_list(items: List, item: any, field: str = None):
    """断言列表中包含指定项"""
    if field:
        values = [i.get(field) for i in items if isinstance(i, dict)]
        assert item in values, f"{item} not found in {field} of items"
    else:
        assert item in items, f"{item} not found in items"
```

---

### 3.5 自动化测试脚本结构

### 3.5.1 测试框架建议

- **接口测试框架**：使用 `pytest` + `requests` 库
- **数据库操作**：使用 `SQLAlchemy` 或 `psycopg2`（PostgreSQL）或数据库迁移工具
- **测试数据准备**：使用 `pytest.fixture` 管理测试数据和数据库连接
- **断言库**：使用 `pytest` 内置断言（基于Python的 `assert`）
- **测试报告**：使用 `pytest-html` 生成HTML报告，`pytest-cov` 生成覆盖率报告

### 3.5.2 测试脚本示例结构

```python
# tests/workbench/test_api.py

import pytest
import requests
from typing import Dict
import psycopg2
from psycopg2.extras import RealDictCursor

# 测试配置
BASE_URL = "http://localhost:8000"
DB_CONFIG = {
    "host": "localhost",
    "port": 5432,
    "database": "test_db",
    "user": "test_user",
    "password": "test_password"
}


@pytest.fixture(scope="session")
def db_connection():
    """数据库连接fixture"""
    conn = psycopg2.connect(**DB_CONFIG)
    yield conn
    conn.close()


@pytest.fixture(scope="function")
def cleanup_test_data(db_connection):
    """清理测试数据"""
    # 测试前清理
    cleanup_sql = """
    DELETE FROM user_notification_view_record WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM user_interface_state WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM alert_to_notification WHERE id >= 6001;
    DELETE FROM system_notification WHERE id >= 7001;
    DELETE FROM risk_alert WHERE id >= 5001;
    DELETE FROM instruction WHERE id >= 4001;
    DELETE FROM hedge_plan WHERE id >= 3001;
    DELETE FROM user_role WHERE user_id >= 1001 AND user_id <= 1008;
    DELETE FROM role WHERE id >= 2001;
    DELETE FROM hedge_user WHERE id >= 1001 AND id <= 1008;
    """
    with db_connection.cursor() as cursor:
        cursor.execute(cleanup_sql)
        db_connection.commit()
    
    yield
    
    # 测试后清理
    with db_connection.cursor() as cursor:
        cursor.execute(cleanup_sql)
        db_connection.commit()


@pytest.fixture(scope="function")
def prepare_test_data(db_connection, cleanup_test_data):
    """准备测试数据"""
    with open("tests/fixtures/test_data.sql", "r", encoding="utf-8") as f:
        sql = f.read()
    
    with db_connection.cursor() as cursor:
        cursor.execute(sql)
        db_connection.commit()
    
    yield


@pytest.fixture(scope="function")
def admin_auth_token(prepare_test_data):
    """获取管理员token"""
    response = requests.post(
        f"{BASE_URL}/api/auth/login",
        json={
            "username": "测试管理员",
            "password": "password123"
        }
    )
    assert response.status_code == 200
    data = response.json()
    return data.get("data", {}).get("token")


@pytest.fixture(scope="function")
def bm_auth_token(prepare_test_data):
    """获取业务经理token"""
    response = requests.post(
        f"{BASE_URL}/api/auth/login",
        json={
            "username": "测试业务经理",
            "password": "password123"
        }
    )
    assert response.status_code == 200
    data = response.json()
    return data.get("data", {}).get("token")


class TestWorkbenchAPI:
    """工作台接口测试类"""
    
    def test_admin_level1_menus(self, admin_auth_token):
        """测试超级管理员的一级目录"""
        headers = {"Authorization": f"Bearer {admin_auth_token}"}
        response = requests.get(
            f"{BASE_URL}/api/workbench/menus/level1",
            headers=headers
        )
        
        assert response.status_code == 200
        data = response.json().get("data", {})
        menus = data.get("menus", [])
        
        assert "全局设置" in menus
        assert len(menus) == 7
        assert all(menu in menus for menu in [
            "合同宝", "指令宝", "匹配宝", "交易宝", 
            "风控宝", "报表", "全局设置"
        ])
    
    def test_business_manager_level1_menus(self, bm_auth_token):
        """测试业务经理的一级目录"""
        headers = {"Authorization": f"Bearer {bm_auth_token}"}
        response = requests.get(
            f"{BASE_URL}/api/workbench/menus/level1",
            headers=headers
        )
        
        assert response.status_code == 200
        data = response.json().get("data", {})
        menus = data.get("menus", [])
        
        assert len(menus) == 1
        assert "合同宝" in menus
        assert "全局设置" not in menus
    
    # 更多测试用例...


# 独立测试函数示例
def test_multi_role_user_menus(bm_auth_token):
    """测试多角色用户的一级目录"""
    headers = {"Authorization": f"Bearer {bm_auth_token}"}
    response = requests.get(
        f"{BASE_URL}/api/workbench/menus/level1",
        headers=headers
    )
    
    assert response.status_code == 200
    data = response.json().get("data", {})
    menus = data.get("menus", [])
    
    assert "交易宝" in menus
    assert "合同宝" in menus
    assert len(menus) == 2
```

---

### 3.6 CI/CD集成

#### 3.6.1 测试执行流程

1. **环境准备**
   - 启动测试数据库
   - 运行数据库迁移
   - 准备测试数据

2. **执行测试**
   - 运行单元测试
   - 运行接口测试
   - 生成测试报告

3. **结果处理**
   - 生成测试覆盖率报告
   - 上传测试报告到CI平台
   - 失败时发送通知

#### 3.6.2 测试依赖配置（requirements-test.txt）

```txt
# 测试依赖
pytest==7.4.0
pytest-html==3.2.0
pytest-cov==4.1.0
pytest-xdist==3.3.1
requests==2.31.0
psycopg2-binary==2.9.7
sqlalchemy==2.0.20
```

#### 3.6.3 pytest配置文件（pytest.ini）

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = 
    --html=reports/test_report.html
    --self-contained-html
    --cov=app
    --cov-report=html:reports/coverage
    --cov-report=xml:reports/coverage.xml
    --cov-report=term-missing
    -v
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    api: marks tests as API tests
```

#### 3.6.4 CI配置文件示例（GitLab CI）

```yaml
# .gitlab-ci.yml

stages:
  - test

api-test:
  stage: test
  image: python:3.11
  services:
    - postgres:13
  variables:
    DATABASE_URL: postgresql://test:test@postgres:5432/test_db
    PYTHONPATH: $CI_PROJECT_DIR
  before_script:
    - pip install --upgrade pip
    - pip install -r requirements-test.txt
    - python -m pytest --setup-only  # 检查测试是否可以运行
  script:
    - python scripts/prepare_test_db.py  # 准备测试数据库
    - pytest tests/ --junitxml=reports/junit.xml
    - pytest tests/ --cov --cov-report=xml
  artifacts:
    reports:
      junit: reports/junit.xml
    paths:
      - reports/
      - htmlcov/
    expire_in: 30 days
  coverage: '/TOTAL.*\s+(\d+%)$/'
  only:
    - merge_requests
    - main
```

#### 3.6.5 测试执行脚本（scripts/run_tests.sh）

```bash
#!/bin/bash
# 测试执行脚本

set -e

echo "=== 准备测试环境 ==="
# 激活虚拟环境（如果使用）
# source venv/bin/activate

echo "=== 安装测试依赖 ==="
pip install -r requirements-test.txt

echo "=== 准备测试数据库 ==="
python scripts/prepare_test_db.py

echo "=== 运行测试 ==="
pytest tests/ \
    --html=reports/test_report.html \
    --self-contained-html \
    --cov=app \
    --cov-report=html:reports/coverage \
    --cov-report=term-missing \
    -v

echo "=== 测试完成 ==="
echo "测试报告: reports/test_report.html"
echo "覆盖率报告: reports/coverage/index.html"
```

---

### 3.7 测试结果报告格式

#### 3.7.1 测试结果表格

| 用例编号 | 模块 | 场景 | 测试数据 | 接口调用 | 预期结果 | 实际结果 | 状态 | 备注 |
|---------|------|------|---------|---------|---------|---------|------|------|
| TEST-001 | 工作台 | 一级目录权限 | 用户1001（超级管理员） | GET /api/workbench/menus/level1 | 返回7个目录 | 返回7个目录 | ✅ 通过 | - |
| TEST-002 | 工作台 | 三级目录显示 | 用户1004（业务经理） | GET /api/workbench/instruction-workspace/level3-menus | 返回业务经理工作台 | 返回业务经理工作台 | ✅ 通过 | - |
| TEST-003 | 工作台 | 指令列表过滤 | 用户1004（业务经理） | GET /api/workbench/instruction-workspace/bm-workspace/instructions | 返回3条指令 | 返回3条指令 | ✅ 通过 | - |

#### 3.7.2 测试报告输出

测试完成后应生成以下报告：

1. **测试执行报告**（JSON/XML格式）
   - 用例执行情况
   - 通过/失败统计
   - 执行时间

2. **测试覆盖率报告**（HTML格式）
   - 接口覆盖率
   - 代码行覆盖率
   - 分支覆盖率

3. **失败用例详情报告**
   - 失败的接口响应
   - 错误堆栈信息
   - 预期vs实际对比

---

## 四、版本历史

### V1.0（2024-01-XX）

**初始版本**

- ✅ 完成个人工作台测试方案设计
- ✅ 明确测试方法和测试范围
- ✅ 编写完整的测试用例（10个主要测试用例）
- ✅ 设计测试数据准备方案
- ✅ 编写自动化测试脚本示例
- ✅ 完成CI/CD集成方案

**测试覆盖**：
1. 一级目录权限测试
2. 指令工作台三级目录测试
3. 指令列表权限过滤测试
4. 通知列表查询测试
5. 通知处理操作测试
6. 红点提醒测试
7. 界面状态保存和恢复测试
8. 手机分辨率适配测试
9. 通知列表分页和筛选测试
10. 批量处理通知测试

**文档状态**：✅ 已定稿，可用于测试实施

---

## 附录：参考信息

### 相关文档

- 0_系统缺省默认.md：设备响应式布局判断、界面状态保存
- 0_全局设置.md：角色与权限管理
- 3_指令宝.md：指令权限设置、各岗位工作台的指令界面
- 7_风控宝.md：风控告警记录、红点提醒规则

### 相关概念

- **指令工作台**：根据不同角色提供对应的指令处理界面
- **通知列表**：统一展示告警转通知和系统通知
- **角色权限**：根据用户角色控制一级目录和数据的访问权限
- **手机分辨率适配**：针对手机设备的界面和功能适配

---

本测试方案作为个人工作台模块的完整测试说明，测试人员应严格按照本方案执行测试，确保功能正确性和数据权限控制的准确性。