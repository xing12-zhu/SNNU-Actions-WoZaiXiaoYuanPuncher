# WoZaiXiaoYuanPuncher-Actions

我在校园自动打卡程序：[zimin9/WoZaiXiaoYuanPuncher](https://github.com/zimin9/WoZaiXiaoYuanPuncher) 的 Github Action 版。

## 关于本脚本

- 添加脚本 `wzxy-healthcheck.py`，适配部分学校的打卡项目“健康打卡”。

  > 关于本项目中的脚本：
  > 
  > - `wzxy-healthcheck.py`，对应打卡项目“健康打卡”（一天只需打卡一次，仅需提交位置信息）。
  >
  > 请按需启用，详见下方使用指南。

## 使用指南

### 写在前面
对于SNNU计科院的同学，可使用以下参数（长安校区）：

answers:["0"]
latitude:34.15095458984375
longitude:108.88822970920138
country:中国
city:西安市
district:长安区
province:陕西省
township:郭杜街道
street:昆明湖路
areacodc:610116

### Step0 准备工作

- 对小程序进行抓包，抓取自己的打卡数据，请参见文末[抓包教程](#抓包教程)。
- 在小程序 我的-设置 中修改自己的密码。
  - 注意：密码为6-12位
  - 修改完成后请勿马上重新登陆

### Step1 Fork本仓库

![step1.1](https://i.loli.net/2021/08/07/CXA4LBzFKxpkYj8.png)

- 点击本仓库页面中右上角的 `Fork` 按钮。

- 稍等片刻，将自动跳转至新建的仓库。

### Step2 配置打卡参数

> 如需配置多用户，请参考文末“其他需求”中的介绍。

- 在新建的仓库页面，点击选项 `Settings`，进入项目仓库设置页面。

- 在左方侧边栏点击选项 `Environments`，点击右上角按钮 `New environment` 创建用于存放用户配置的 Environment，命名为 `WZXY_CONFIG_01`。

![step2.1](https://i.loli.net/2021/08/07/jPYLRtgVk27KAUl.png)

- 进入新建的 Environment ，在 “Environment Secrets” 一栏中点击 `Add Secret` 按钮，根据需要新建下列的 Secret，并填写对应 Value 值：
  <details>
  <summary><b>基本参数</b></summary>
  
  > 由于不同的学校情况与实际需求，以下数据需要自行抓取。

  - `USERNAME`：我在校园账号的用户名。

  - `PASSWORD`：我在校园账号的密码。

  - `CACHE_NAME`：值任意，用于储存 jwsession 的缓存文件的前缀名。为避免信息泄露，建议使用包含数字与大小写英文的无序字符串，且长度在32位以上（可以尝试键盘乱打 or 使用生成器）。
    > 请注意：配置多账户打卡时，不同环境中的`CACHE_NAME`不能相同！！

  - `TEMPERATURE`（可选）：打卡提交体温信息时使用的体温值，数值要求精确到1位小数。可以仅指定一个温度值（例：`36.0`），也可以指定温度值范围，两个温度值间使用符号`~`连接（例：`36.1~36.3`），打卡时将随机从指定的范围中选取一个值作为体温数据提交。如不创建该 Secret，脚本将使用默认值`36.0~36.5`。

  - `ANSWERS`（可选）：打卡时所提交的选项回答，对应抓包信息中的“answers”。可以通过该 Secrect 自定义打卡问题的选项或回答。

  - `LATITUDE`：打卡该项目时所提交位置信息的纬度，对应抓包信息中的 “latitude”。
  
  - `LONGITUDE`：打卡该项目时所提交位置信息的经度，对应抓包信息中的 “longitude”。
  
  - `COUNTRY`：打卡该项目时所提交位置信息的经度，对应抓包信息中的 “country”。
  
  - `CITY`：打卡该项目时所提交位置信息的市，对应抓包信息中的 “city”。
  
  - `DISTRICT`：打卡该项目时所提交位置信息的区，对应抓包信息中的 “district”。
  
  - `PROVINCE`：打卡该项目时所提交位置信息的省，对应抓包信息中的 “province”。
  
  - `TOWNSHIP`：打卡该项目时所提交位置信息的街道，对应抓包信息中的 “township” 。
  
  - `STREET`：Value 值填写 打卡该项目时所提交位置信息的路，对应抓包信息中的 “street”。
  
  > 如果需要两个打卡项目所需提交的地理位置信息不一样（比如“日检日报”在校打卡，“健康打卡”在家打卡），可以分别抓取两个打卡项目的提交数据，并参考文末“常见问题 - 3.如何配置多账户/多地点打卡？” 新建 Environment 并配置对应 Secect。

  </details>

  <details>
  <summary><b>推送服务（可选）</b></summary>

  目前支持的推送方式：
  - Serverchan-Turbo
  - Bark
  - pushplus
  - 钉钉机器人
  - QQ机器人（go-cqhttp）

  需要使用哪一种方式推送，创建该方式对应的 Secret 即可。

  可以同时推送多个渠道，只需额外创建这些推送方式对应的 Secret 即可。
  
  如不创建这些推送方式对应的 Secret，则不会推送打卡结果通知。

  <details>
  <summary><b>Serverchan-Turbo</b></summary>

  - `SCT_KEY`（可选）：填写自己 [Serverchan-Turbo](https://sct.ftqq.com/sendkey) 的 SendKey，用于 Serverchan-Turbo 推送打卡结果的通知。

  </details>

  <details>
  <summary><b>Bark</b></summary>

  - `BARK_TOKEN` （可选）：填写自己 Bark 的推送 URL。
    > 形如 `http://yourdomain.name/thisisatoken`，用于 Bark 推送打卡结果的通知；**请注意不要以斜杠结尾。**
    >
    > 为避免输入错误，建议从 Bark 客户端直接复制。
  
  </details>

  <details>
  <summary><b>pushplus</b></summary>

  - `PUSHPLUS_TOKEN`（可选）：填写自己 [pushplus](https://www.pushplus.plus/) 的 token，用于 pushplus 推送打卡结果的通知。
    > ⚠本脚本使用的 pushplus 地址为[www.pushplus.plus](https://www.pushplus.plus)，并非百度搜索的第一个结果，请注意甄别。

  </details>

  <details>
  <summary><b>喵提醒</b></summary>

  - `MIAO_CODE` （可选）：填写 [喵提醒](https://miaotixing.com/) 的喵码，需要先创建提醒获取，具体见喵推送公众号，用于 喵提醒 推送打卡结果的通知。

  </details>

  <details>
  <summary><b>钉钉机器人</b></summary>

  - `DD_BOT_ACCESS_TOKEN`（可选）：钉钉机器人推送 Token，填写机器人的 Webhook 地址中的 token。只需 `https://oapi.dingtalk.com/robot/send?access_token=XXX` 等于=符号后面的XXX即可。

  - `DD_BOT_SECRET`（可选）：钉钉机器人推送SECRET。[官方文档](https://developers.dingtalk.com/document/app/custom-robot-access)
    > 如需配置钉钉机器人，上述的 `DD_BOT_ACCESS_TOKEN` 和 `DD_BOT_SECRET` 两条 Secrect 都需创建。

  </details>

  <details>
  <summary><b>QQ机器人（go-cqhttp）</b></summary>

  - `GOBOT_URL`（可选）：go-cqhttp 推送的 URL
    > 推送到个人QQ：`http://127.0.0.1/send_private_msg`
    >
    > 推送到群：`http://127.0.0.1/send_group_msg`

  - `GOBOT_TOKEN`（可选）：填写在go-cqhttp文件设置的访问密钥`access-token`，可不填

  - `GOBOT_QQ`（可选）：go-cqhttp如果GOBOT_URL设置 /send_private_msg 则需要填入 user_id=个人QQ，相反如果是 /send_group_msg 则需要填入 group_id=QQ群
    > 如需配置基于 go-cqhttp 的 QQ 机器人推送，上述的 `GOBOT_URL` 和 `GOBOT_QQ` 为必填项，`GOBOT_TOKEN`可为空。
    >
    > 详情请参考：[go-cqhttp相关API](https://docs.go-cqhttp.org/api)
  
  </details>

### Step3 配置脚本运行时间

脚本的触发运行时间由项目仓库内`.github/workflows`的 Workflow 文件配置：
  
- `wzxy_healthcheck.yml`

  - 对应脚本“`wzxy-healthcheck.py`”（打卡项目“健康打卡”）。
  
  - 默认在每天北京时间 7:30 和 20:30 执行。

如果需要修改脚本的运行时间：

![step3.1](https://i.loli.net/2021/08/07/dNeS2igbwKmPzCO.png)

- 点击页面上方选项 `Code`，回到项目仓库主页。

- 点击文件夹`.github/workflows`，修改所需要的 Workflow 文件。

  以修改`wzxy_healthcheck.yml`为例：

  - 点击`wzxy_healthcheck.yml`，进入文件预览。

  - 点击预览界面右上方笔的图标，进入编辑界面。

    ![step3.2](https://i.loli.net/2021/08/07/mvgOB824MsdZ1up.png)

  - 根据自己的打卡时间需要，修改代码中的 cron 表达式：
    > cron是个啥？百度一下！

    ![step3.3](https://i.loli.net/2021/08/07/ntImHFAeu6TM7zK.png)

    > **定时注意事项：**
    >
    > - Github Actions 用的是世界标准时间（UTC），北京时间（UTC+8）转换为世界标准时需要减去8小时。
    > - Github Action 执行计划任务需要排队，脚本并不会准时运行，大概会延迟1h左右，请注意规划时间。

- 修改完成后，点击页面右侧绿色按钮 `Start commit`，然后点击绿色按钮 `Commit changes`。

  > **注意：**
  >
  > 出于开发者个人使用需要，`wzxy_healthcheck.yml`里设定的`environment`参数默认为`environment: WZXY_CONFIG_02`；
  >
  > 如果你严格按照上述教程操作且没有多账户/多地点打卡需要，请找到该行代码并将02改为01。
  >
  > 关于多账户/多配置文件的设置，请参考文末“常见问题”

### Step4 手动测试脚本运行

- 点击页面上方选项 `Actions`，进入 Github Actions 配置页面。

- 左侧边栏点击需要测试的脚本：
  - `WZXY_HealthCheck`：对应脚本“`wzxy-healthcheck.py`”，打卡项目“健康打卡”。

以测试 `WZXY_DailyReport` 为例：

![step4.1](https://i.loli.net/2021/08/07/qWERC7NUDuvxPd2.png)

- 在未自行打卡的打卡时段，点击右侧按钮 `Run workflow`，再次点击绿色按钮 `Run workflow`。

- 等待几秒后刷新页面。

- 2分钟后登入我在校园小程序；如无意外，打卡将被完成；如果你正确配置了 PUSH_TOKEN，应同时在2分钟内收到微信消息推送。

- 如果出现以下情况：
  - 2分钟后仍未自动打卡。
  - Github Actions 界面最新的 workflow run `WZXY_HealthCheck` 状态为红色错误。
  - 以及其他错误情况。

  请在Github Actions 配置界面中，打开最新的 Workflow run `WZXY_HealthCheck`，查看错误日志，并检查自己的参数配置是否正确。

> 两个脚本对应的 Workflow 都默认开启定时执行任务，如果你无需使用/需要暂时停用某一脚本，请参照以下步骤停用其对应的 Workflow：
>
> ![step4.2](https://i.loli.net/2021/08/07/W23K7Gqzsra59Xf.png)
>
> - 在 Github Actions 配置页面中，左侧边栏选择需要停用的脚本所对应的 Workflow。
> - 点击搜索栏右边的 `...` 按钮，然后点击 `Disable workflow`。

## 常见问题

1. 即便所配置的密码正确，脚本执行时仍然提示`用户名或密码错误，还可尝试*次`？
   - 在小程序中重新修改密码。
   - 修改密码后不要马上在小程序上重新登陆。
   - 更新对应用户配置文件中的 Secret `PASSWORD`，填写新密码。
   - 再次尝试运行脚本，查看是否正常登陆并获取 jwsession。
   - 如仍有问题，请在确保配置文件中密码信息正确的后提 issue。

2. 日检日报提交的选项不对？/ 提示`服务出错(500)`？
   - 请参照 Step2 中的介绍，创建并填写 Secrect `ANSWERS`。

3. 如何配置多账户/多地点打卡？
   - 参照 Step 2，新建并配置另一环境；环境名建议保持 `WZXY_CONFIG_**`的格式。
   - 参考 Step 3，打开打卡脚本所对应的 workflow 文件，复制末尾的多账户配置示例代码，注销注释后填写另一配置的环境名即可。

4. 打卡不准时？
   - Github Action服务器使用的时间是UTC，设置定时时请注意转换为北京时间（UTC+8）。
   - Github Action执行计划任务需要排队，并不会准时运行脚本，大概会延迟1h左右，请注意规划时间。

5. 需要其他推送通知渠道？
   - 请提 issue 或参考代码自行实现。

6. 其他问题？
   - 欢迎提 issue。

## 抓包大致方法

![cap-howto](https://i.loli.net/2021/08/07/VBrtzGnQEJc5XF4.png)

- 在电脑上安装配置好 Fiddler。

- 启动微信电脑版和 Fiddler，打开我在校园小程序，先手动打卡一次日检日报/健康打卡。

- 提交打卡信息的同时观察 Fiddler 左侧栏中最新出现的 Host 为 `student.wozaixiaoyuan.com` 的信息（如果打卡的是日检日报，URL 为`/heat/save.json`；健康打卡则为`/health/save.json"`）。

- 双击打开这条信息，然后点击右侧上方的 `WebForms` 一栏，对照显示抓取到的信息填写 Environment Secrets 就可以了。

- Fiddler 配置与抓包操作参考：

  - [Chaney1024/wozaixiaoyuan](Chaney1024/wozaixiaoyuan)
  
  - [Duangdi/fuck-wozaixiaoyuan](https://github.com/Duangdi/fuck-wozaixiaoyuan/blob/master/%E4%B8%80%E6%97%A5%E4%B8%89%E6%A3%80%E8%87%AA%E5%8A%A8%E6%89%93%E5%8D%A1.pdf)
  
  - [Liuism/xsyu-wzxy-sign](https://github.com/Liuism/xsyu-wzxy-sign)

## Todo

- [ ] 解耦推送模块

## 参考/致谢

- [zimin9/WoZaiXiaoYuanPuncher](https://github.com/zimin9/WoZaiXiaoYuanPuncher)

- [Chorer/WoZaiXiaoYuanPuncher-cloudFunction](https://github.com/Chorer/WoZaiXiaoYuanPuncher-cloudFunction)

- [why20hh/WoZaiXiaoYuan-SVTCC](https://github.com/why20hh/WoZaiXiaoYuan-SVTCC) ，健康打卡脚本参考了其代码。

- [@LeslieLeung](https://github.com/LeslieLeung) ，贡献了对多种通知方式（Serverchan-Turbo、PushPlus、Bark）的支持。

## 声明

- 本项目仅供编程学习/个人使用，请遵守Apache-2.0 License开源项目授权协议.
  

- 请在国家法律法规和校方相关原则下使用。

- 开发者不对任何下载者和使用者的任何行为负责。

- 程序使用的所有信息均利用 Github 的 [Secrets](https://docs.github.com/cn/actions/reference/encrypted-Secrets) 加密储存。
