---
title: AliceNetwork开发者云API封装
date: 2025-04-13 04:59:07
tags: []
categories: 默认
---

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

从 [控制台](https://app.alice.ws/ephemera/console) 获取 `API Token`
在同级目录新建 `kp-api-token.txt` 并写入token

```python
from AliceAPI import EphemeraAPI


def GetApiToken():
    with open("kp-api-token.txt", "r", encoding="utf-8") as token_file:
        api_token = token_file.read()
    return api_token


Ephemera = EphemeraAPI(GetApiToken())

# ------------------------------------------------------
# InstanceList
print(Ephemera.InstanceList())


# ------------------------------------------------------
# PlanList
print(Ephemera.PlanList())


# ------------------------------------------------------
# PlanOsList
plans = Ephemera.PlanList()
print(Ephemera.PlanOsList(plans["LAX.Evo.Micro"]))


# ------------------------------------------------------
# InstanceDeploy
plans = Ephemera.PlanList()
oss = Ephemera.PlanOsList(plans["LAX.Evo.Micro"])
print(
    Ephemera.InstanceDeploy(
        plans["LAX.Evo.Micro"], oss["Debian 12 (Bookworm) Minimal"], hours=1
    )
)


# ------------------------------------------------------
# InstanceDestroy
plans = Ephemera.PlanList()
oss = Ephemera.PlanOsList(plans["LAX.Evo.Micro"])
instance_id = Ephemera.InstanceDeploy(
    plans["LAX.Evo.Micro"], oss["Debian 12 (Bookworm) Minimal"], hours=1
)["id"]
print(Ephemera.InstanceDestroy(instance_id))


# ------------------------------------------------------
# InstancePower
plans = Ephemera.PlanList()
oss = Ephemera.PlanOsList(plans["LAX.Evo.Micro"])
instance_id = Ephemera.InstanceDeploy(
    plans["LAX.Evo.Micro"], oss["Debian 12 (Bookworm) Minimal"], hours=1
)["id"]
print(Ephemera.InstancePower(instance_id, "boot"))  # boot shutdown restart poweroff


# ------------------------------------------------------
# InstanceRebuild
plans = Ephemera.PlanList()
oss = Ephemera.PlanOsList(plans["LAX.Evo.Micro"])
instance_id = Ephemera.InstanceDeploy(
    plans["LAX.Evo.Micro"], oss["Debian 12 (Bookworm) Minimal"], hours=1
)["id"]
print(Ephemera.InstanceRebuild(instance_id, oss["AlmaLinux 9 Latest"]))

# ------------------------------------------------------
# InstanceRenewal
plans = Ephemera.PlanList()
oss = Ephemera.PlanOsList(plans["LAX.Evo.Micro"])
instance_id = Ephemera.InstanceDeploy(
    plans["LAX.Evo.Micro"], oss["Debian 12 (Bookworm) Minimal"], hours=1
)["id"]
print(Ephemera.InstanceRenewal(instance_id, hours=1))
```
