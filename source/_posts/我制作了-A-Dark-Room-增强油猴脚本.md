---
title: 我制作了 A Dark Room 增强油猴脚本
date: 2026-02-08 06:46:48
---

```javascript
// ==UserScript==
// @name         Batter ADR
// @version      2026-02-08
// @description  A Dark Room 体验增强
// @author       runoneall
// @require      https://cdn.jsdelivr.net/npm/lil-gui@0.21
// @grant        none
// @match        *://*/*
// ==/UserScript==

const task = {
    _tasks: [],
    _isRunning: false,

    add(condition, action) {
        this._tasks.push({ condition, action });
        this._bootstrap();
    },

    _bootstrap() {
        if (this._isRunning) return;
        this._isRunning = true;
        this._loop();
    },

    _loop() {
        if (this._tasks.length === 0) {
            this._isRunning = false;
            return;
        }

        for (let i = this._tasks.length - 1; i >= 0; i--) {
            const { condition, action } = this._tasks[i];

            try {
                if (condition()) {
                    action();
                    this._tasks.splice(i, 1);
                }
            } catch (e) {
                console.error("Task execution failed:", e);
                this._tasks.splice(i, 1);
            }
        }

        requestAnimationFrame(() => this._loop());
    },
};

const pack = (target, ui) => {
    const proxy = new Proxy(target, {
        set: (obj, key, value) => {
            const isNew = !(key in obj);
            obj[key] = value;

            if (isNew) {
                ui.add(obj, key).name(key).listen();
            }

            return true;
        },
    });

    Object.keys(target).forEach((key) => {
        ui.add(target, key).name(key).listen();
    });

    return proxy;
};

const init = () => {
    const gui = new lil.GUI({ title: "A Dark Room 体验增强" });
    const blocker = (e) => e.stopPropagation();
    const dom = gui.domElement;

    gui.add(State, "version").name("ADR 版本").disable();

    Object.assign(dom.style, {
        top: "auto",
        bottom: "15px",
        left: "15px",
        right: "auto",
    });

    ["keydown", "keyup", "keypress"].forEach((type) =>
        dom.addEventListener(type, blocker, true),
    );

    dom.addEventListener(
        "mousedown",
        (e) => {
            if (e.target.tagName === "INPUT") {
                e.target.focus();
                e.stopPropagation();
            }
        },
        true,
    );

    task.add(
        () => State.stores && Object.keys(State.stores).length > 0,
        () => {
            console.log("Batter ADR: 检测到 State.stores, 注入物品破解");
            State.stores = pack(State.stores, gui.addFolder("仓库物品"));
        },
    );

    task.add(
        () =>
            typeof Path !== "undefined" &&
            Path.outfit &&
            Object.keys(Path.outfit).length > 0,
        () => {
            console.log("Batter ADR: 检测到 Path.outfit, 注入冒险破解");
            Path.outfit = pack(Path.outfit, gui.addFolder("冒险物品"));
        },
    );

    task.add(
        () => typeof World !== "undefined" && World.updateSupplies,
        () => {
            console.log(
                "Batter ADR: 检测到 World.updateSupplies, 注入冒险辅助",
            );
            setInterval(() => {
                World.BASE_WATER = 10000;
                World.BASE_HEALTH = 10000;
                World.water = World.getMaxWater();
                World.health = World.getMaxHealth();
                World.updateSupplies();
            }, 100);
        },
    );

    task.add(
        () => typeof Path !== "undefined" && Path.getWeight,
        () => {
            console.log("Batter ADR: 检测到 Path.getWeight, 注入背包破解");
            Path.getWeight = function (item) {
                return 0;
            };
        },
    );

    task.add(
        () => typeof Button !== "undefined" && Button.cooldown,
        () => {
            console.log("Batter ADR: 检测到 Button.cooldown, 注入武器增强");

            if (!Button._originalCooldown) {
                Button._originalCooldown = Button.cooldown;
            }

            Button.cooldown = function (btn, option) {
                const headerButtons =
                    document.getElementsByClassName("headerButton");
                const isAdventuring =
                    headerButtons.length > 2 &&
                    headerButtons[2].classList.contains("selected");

                if (isAdventuring && btn && btn.hasClass("weaponButton")) {
                    Button.clearCooldown(btn, true);

                    $("div.cooldown", btn).stop(true, true).width(0);
                    btn.removeClass("disabled");
                    btn.data("onCooldown", false);

                    return;
                }

                return Button._originalCooldown.apply(this, arguments);
            };

            if (!Button._originalSetDisabled) {
                Button._originalSetDisabled = Button.setDisabled;
            }

            Button.setDisabled = function (btn, disabled) {
                const headerButtons =
                    document.getElementsByClassName("headerButton");
                const isAdventuring =
                    headerButtons.length > 2 &&
                    headerButtons[2].classList.contains("selected");

                if (isAdventuring && btn && btn.hasClass("weaponButton")) {
                    btn.removeClass("disabled");
                    btn.data("disabled", false);
                    btn.data("onCooldown", false);
                    return;
                }

                return Button._originalSetDisabled.apply(this, arguments);
            };
        },
    );
};

(function () {
    "use strict";

    task.add(
        () =>
            typeof lil !== "undefined" &&
            typeof State !== "undefined" &&
            State.version,
        () => {
            console.log("Batter ADR: 检测到 State.version, 注入增强程序");
            init();
        },
    );
})();
```
