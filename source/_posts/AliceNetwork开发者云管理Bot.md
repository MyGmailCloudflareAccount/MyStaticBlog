---
title: AliceNetwork开发者云管理Bot
date: 2025-04-19 05:46:03
tags: []
categories: 默认
---

云湖机器人（需下载云湖APP）

效果图：
![](https://s.rmimg.com/2025-04-19/1745027138-305955-2025-04-19-093942.png)
![](https://s.rmimg.com/2025-04-19/1745027141-233577-2025-04-19-094122.png)
![](https://s.rmimg.com/2025-04-19/1745027143-928952-2025-04-19-094225.png)

直接体验：
https://yhfx.jwznb.com/share?key=YZInMjHRY6c2&ts=1745027189

部署（需python3.12）

依赖包：

```plaintext
blinker==1.9.0
certifi==2025.1.31
charset-normalizer==3.4.1
click==8.1.8
filetype==1.2.0
Flask==3.1.0
idna==3.10
itsdangerous==2.2.0
Jinja2==3.1.6
MarkupSafe==3.0.2
requests==2.32.3
urllib3==2.4.0
Werkzeug==3.1.3
```

部署sdkFrame

```shell
git clone https://github.com/runoneall/sdkFrame.git
python3 -m sdkFrame -add-origin https://dev.oneall.eu.org/ryhsdk2/map.json -update-origin -set-env YUNHU_TOKEN=云湖机器人token -set-env 'SERVER=json:{   1 ↵
    "host": "0.0.0.0",
    "port": 端口,
    "debug": true,
    "threaded": true
  }'
python3 -m sdkFrame -install-module SendMessage@r1a-ryhsdk2
python3 -m sdkFrame -install-module ServCommand@r1a-ryhsdk2 -install-module ServShortCut@r1a-ryhsdk2
```

`AliceAPI.py`

```python
import requests


class EphemeraAPI:
    def __init__(self, api_token):
        self.EVO_API_BASE = "https://app.alice.ws/cli/v1/Evo/"
        self.api_header = {"KP-APIToken": api_token}

    def EvoJob(self, job_name):
        return self.EVO_API_BASE + job_name

    def InstanceList(self):
        resp = requests.get(self.EvoJob("Instance"), headers=self.api_header).json()
        if resp["status"] != 200:
            return None
        return resp["data"]

    def PlanList(self):
        resp = requests.get(self.EvoJob("Plan"), headers=self.api_header).json()
        if resp["status"] != 200:
            return None
        PlanMap = {}
        for plan in resp["data"]:
            PlanMap[plan["name"]] = plan["id"]
        return PlanMap

    def PlanOsList(self, plan_id):
        resp = requests.post(
            self.EvoJob("PlanToOS"),
            headers=self.api_header,
            data={"plan_id": str(plan_id)},
        ).json()
        if resp["status"] != 200:
            return None
        OSMap = {}
        for _, osGroup in resp["data"].items():
            for osItem in osGroup["os"]:
                OSMap[osItem["name"]] = osItem["id"]
        return OSMap

    def InstanceDeploy(self, plan_id, os_id, hours):
        resp = requests.post(
            self.EvoJob("Deploy"),
            headers=self.api_header,
            data={"product_id": str(plan_id), "os_id": str(os_id), "time": str(hours)},
        ).json()
        if resp["status"] != 200:
            return None
        return resp["data"]

    def InstanceDestroy(self, instance_id):
        resp = requests.post(
            self.EvoJob("Destroy"),
            headers=self.api_header,
            data={"id": str(instance_id)},
        ).json()
        if resp["status"] != 200:
            return False
        return True

    def InstancePower(self, instance_id, action):
        resp = requests.post(
            self.EvoJob("Power"),
            headers=self.api_header,
            data={"id": str(instance_id), "action": str(action)},
        ).json()
        if resp["status"] != 200:
            return False
        return True

    def InstanceRebuild(self, instance_id, os_id):
        resp = requests.post(
            self.EvoJob("Rebuild"),
            headers=self.api_header,
            data={"id": str(instance_id), "os": str(os_id)},
        ).json()
        if resp["status"] != 200:
            return False
        return resp["data"]

    def InstanceRenewal(self, instance_id, hours):
        resp = requests.post(
            self.EvoJob("Renewal"),
            headers=self.api_header,
            data={"id": str(instance_id), "time": str(hours)},
        ).json()
        if resp["status"] != 200:
            return False
        return True
```

到 https://www.yhchat.com/control 创建机器人

快捷菜单设置（全部为普通按钮，事件推送）
名称随意，记录菜单id
![](https://s.rmimg.com/2025-04-19/1745028339-727178-2025-04-19-100458.png)

指令列表
添加一个普通指令和一个自定义输入指令，名称随意，记录id
![](https://s.rmimg.com/2025-04-19/1745028453-377258-2025-04-19-100639.png)

自定义输入表单（名称随意，记录id）
![](https://s.rmimg.com/2025-04-19/1745028539-43368-2025-04-19-100819.png)

```plaintext
Plan选项
LAX.Evo.Micro#LAX.Evo.Standard#LAX.Evo.Pro#LAX.Evo.Ultra

OS选项
Debian 12 (Bookworm) Minimal#Debian 11 (Bullseye) Minimal#Debian 12 DevKit#Ubuntu Server 20.04 LTS Minimal#Ubuntu Server 22.04 LTS Minimal#CentOS 7 Minimal#CentOS Stream 9 Minimal#AlmaLinux 8 Minimal#AlmaLinux 9 Latest#Alpine Linux 3.19
```

`app.py`

```python
import json
import os
import sdkFrame
import AliceAPI

sdk = sdkFrame.init()

if not os.path.exists("db.json"):
    with open("db.json", "w", encoding="utf-8") as f:
        f.write("{}")


def ReadDB():
    with open("db.json", "r", encoding="utf-8") as f:
        return json.load(f)


def WriteDB(data_dict):
    with open("db.json", "w", encoding="utf-8") as f:
        json.dump(data_dict, fp=f, indent=2, ensure_ascii=True)


def SetToken(data):
    event = data["event"]
    if event["chat"]["chatType"] == "bot":
        uid = event["sender"]["senderId"]
        token = event["message"]["content"]["text"]
        WriteDB({**ReadDB(), **{uid: {"token": token}}})
        sdk.SendMessage.Text(uid, "user", f"Set {uid} Token {token}")


def InstanceConfig(data):
    event = data["event"]
    if event["chat"]["chatType"] == "bot":
        uid = event["sender"]["senderId"]
        formJson = event["message"]["content"]["formJson"]
        target_plan = formJson["Plan 选择框表单ID"]["selectValue"]
        target_os = formJson["OS 选择框表单ID"]["selectValue"]
        target_hours = formJson["Hours 输入框表单ID"]["value"]

        db = ReadDB()
        if uid in db and "token" in db[uid]:
            db[uid]["plan"] = target_plan
            db[uid]["os"] = target_os
            db[uid]["hours"] = target_hours
            WriteDB(db)
            sdk.SendMessage.Text(
                uid,
                "user",
                f"""Default Config:
- Plan: {target_plan}
- OS: {target_os}
- Hours: {target_hours}""",
            )
        else:
            sdk.SendMessage.Text(uid, "user", "Set Token First")


def InstanceInfo(data):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "instance_id" not in db[uid]:
            db[uid]["instance_id"] = ""

        if db[uid]["instance_id"] == "":
            sdk.SendMessage.Text(uid, "user", "No Instance Deployed")
            return

        instance_id = db[uid]["instance_id"]
        sdk.SendMessage.Text(uid, "user", f"Checking Instance {instance_id}...")
        resp = AliceAPI.EphemeraAPI(db[uid]["token"]).InstanceList()

        if resp == None:
            sdk.SendMessage.Text(uid, "user", "Check Failed")
            return

        for instance in resp:
            if str(instance["id"]) == instance_id:
                sdk.SendMessage.Text(
                    uid,
                    "user",
                    f"""Server {instance_id}:
- Ipv4: {instance["ipv4"]}
- Ipv6: {instance["ipv6"]}
- Hostname: {instance["hostname"]}
- Cpu Total: {instance["cpu"]} Core
- Cpu Type: {instance["cpu_name"]}
- Memory Total: {instance["memory"]} MB
- Disk Total: {instance["disk"]} GB
- Disk Type: {instance["disk_type"]}
- User: {instance["user"]}
- Password: {instance["password"]}
- Status: {instance["status"]}
- Created At: {instance["creation_at"]}
- Expired At: {instance["expiration_at"]}
- Plan: {instance["plan"]}
- Region: {instance["region"]}
- OS: {instance["os"]}""",
                )
                break


def InstanceDeploy(data):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "plan" not in db[uid] or "os" not in db[uid] or "hours" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Default Config First")
            return

        if "instance_id" in db[uid] and db[uid]["instance_id"] != "":
            sdk.SendMessage.Text(uid, "user", "Instance Already Deployed")
            return

        sdk.SendMessage.Text(uid, "user", "Deploying Instance...")
        target_plan = db[uid]["plan"]
        target_os = db[uid]["os"]
        target_hours = db[uid]["hours"]

        Ephemera = AliceAPI.EphemeraAPI(db[uid]["token"])
        plans = Ephemera.PlanList()
        oss = Ephemera.PlanOsList(plans[target_plan])
        resp = Ephemera.InstanceDeploy(
            Ephemera.PlanList()[target_plan], oss[target_os], hours=int(target_hours)
        )

        if resp == None:
            sdk.SendMessage.Text(uid, "user", "Deploy Failed")
            return

        db[uid]["instance_id"] = str(resp["id"])
        WriteDB(db)
        sdk.SendMessage.Text(
            uid,
            "user",
            f"Instance {resp['id']} Deployed, Please Wait for 2-3 minutes, Use Info Button to Check Status",
        )


def InstanceDestroy(data):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "instance_id" not in db[uid] or db[uid]["instance_id"] == "":
            sdk.SendMessage.Text(uid, "user", "No Instance Deployed")
            return

        instance_id = db[uid]["instance_id"]
        sdk.SendMessage.Text(uid, "user", f"Destroying Instance {instance_id}...")

        resp = AliceAPI.EphemeraAPI(db[uid]["token"]).InstanceDestroy(instance_id)
        if resp == False:
            sdk.SendMessage.Text(uid, "user", "Destroy Failed")
            return

        del db[uid]["instance_id"]
        WriteDB(db)
        sdk.SendMessage.Text(uid, "user", f"Instance {instance_id} Destroyed")


def power_action(data, action):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "instance_id" not in db[uid] or db[uid]["instance_id"] == "":
            sdk.SendMessage.Text(uid, "user", "No Instance Deployed")
            return

        instance_id = db[uid]["instance_id"]
        sdk.SendMessage.Text(
            uid, "user", f"Performing {action} on Instance {instance_id}..."
        )

        resp = AliceAPI.EphemeraAPI(db[uid]["token"]).InstancePower(instance_id, action)
        if resp == False:
            sdk.SendMessage.Text(uid, "user", f"{action} Failed")
            return

        sdk.SendMessage.Text(uid, "user", f"Instance {instance_id} {action}ed")


def InstanceBoot(data):
    power_action(data, "boot")


def InstanceShutdown(data):
    power_action(data, "shutdown")


def InstanceRestart(data):
    power_action(data, "restart")


def InstancePoweroff(data):
    power_action(data, "poweroff")


def InstanceRebuild(data):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "plan" not in db[uid] or "os" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Default Config First")
            return

        if "instance_id" not in db[uid] or db[uid]["instance_id"] == "":
            sdk.SendMessage.Text(uid, "user", "No Instance Deployed")
            return

        instance_id = db[uid]["instance_id"]
        sdk.SendMessage.Text(uid, "user", f"Rebuilding Instance {instance_id}...")

        Ephemera = AliceAPI.EphemeraAPI(db[uid]["token"])
        plans = Ephemera.PlanList()
        oss = Ephemera.PlanOsList(plans[db[uid]["plan"]])
        resp = Ephemera.InstanceRebuild(instance_id, oss[db[uid]["os"]])

        if resp == False:
            sdk.SendMessage.Text(uid, "user", "Rebuild Failed")
            return

        sdk.SendMessage.Text(uid, "user", f"Instance {instance_id} Rebuilt")


def InstanceRenewal(data):
    event = data["event"]
    if event["senderType"] == "user":

        db = ReadDB()
        uid = event["senderId"]

        if uid not in db or "token" not in db[uid]:
            sdk.SendMessage.Text(uid, "user", "Set Token First")
            return

        if "instance_id" not in db[uid] or db[uid]["instance_id"] == "":
            sdk.SendMessage.Text(uid, "user", "No Instance Deployed")
            return

        instance_id = db[uid]["instance_id"]
        sdk.SendMessage.Text(uid, "user", f"Renewing Instance {instance_id}...")

        target_hours = db[uid]["hours"]
        resp = AliceAPI.EphemeraAPI(db[uid]["token"]).InstanceRenewal(
            instance_id, int(target_hours)
        )
        if resp == False:
            sdk.SendMessage.Text(uid, "user", "Renewal Failed")
            return

        sdk.SendMessage.Text(
            uid, "user", f"Instance {instance_id} Renewed {target_hours} hours"
        )


sdk.ServCommand.AddHandle(SetToken, Set Token 指令ID)
sdk.ServCommand.AddHandle(InstanceConfig, Default Config 指令ID)
sdk.ServShortCut.AddHandle(InstanceInfo, "Info 菜单ID")
sdk.ServShortCut.AddHandle(InstanceDeploy, "Deploy 菜单ID")
sdk.ServShortCut.AddHandle(InstanceDestroy, "Destroy 菜单ID")
sdk.ServShortCut.AddHandle(InstanceBoot, "Boot 菜单ID")
sdk.ServShortCut.AddHandle(InstanceShutdown, "Shutdown 菜单ID")
sdk.ServShortCut.AddHandle(InstanceRestart, "Restart 菜单ID")
sdk.ServShortCut.AddHandle(InstancePoweroff, "Poweroff 菜单ID")
sdk.ServShortCut.AddHandle(InstanceRebuild, "Rebuild 菜单ID")
sdk.ServShortCut.AddHandle(InstanceRenewal, "Renewal 菜单ID")
sdk.Server.AddTrigger(sdk.ServCommand)
sdk.Server.AddTrigger(sdk.ServShortCut)

sdk.Server.Start()
```

启动后填入到 `配置消息订阅接口`
