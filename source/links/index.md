---
title: 友链
date: 2026-01-21 11:48:38
---

<script>
    const links = [
        {
            avatar: "https://rdev.qzz.io/favicon.webp",
            link: "https://rdev.qzz.io/",
            name: "Runoneall Develop",
            desc: "我的开发记录、随笔、以及其他...",
        },
        {
            avatar: "https://s.rmimg.com/original/3X/d/4/d431fdb324bedd0b15dbf4d0bb67ec5507c8d831.png",
            link: "https://ybr.flmp.uk/",
            name: "CyanTea的小站",
            desc: "just CyanTea的小站",
        },
        {
            avatar: "https://r2.anran.xyz/avatar.jpg",
            link: "https://www.anran.xyz/",
            name: "YingXinche的小站",
            desc: "像Minecraft的存档一样失去了很久...",
        },
    ];
</script>

<style>
    .links-content {
        margin-top: 1rem;
    }

    .link-navigation::after {
        content: " ";
        display: block;
        clear: both;
    }

    .card {
        /* Default for Desktop: Two columns */
        width: 45%;
        font-size: 1rem;
        padding: 10px 20px;
        border-radius: 4px;
        transition-duration: 0.15s;
        margin-bottom: 1rem;
        display: flex;
        box-sizing: border-box; /* Ensures padding doesn't break width */
    }

    .card:nth-child(odd) {
        float: left;
    }

    .card:nth-child(even) {
        float: right;
    }

    /* Mobile Optimization */
    @media screen and (max-width: 768px) {
        .card {
            width: 100%; /* Full width on mobile */
            float: none !important; /* Stop floating side-by-side */
            margin-right: 0;
            margin-left: 0;
        }
    }

    .card:hover {
        transform: scale(
            1.05
        ); /* Slightly reduced scale for better mobile feel */
        box-shadow:
            0 2px 6px 0 rgba(0, 0, 0, 0.12),
            0 0 6px 0 rgba(0, 0, 0, 0.04);
    }

    .card a {
        border: none;
    }

    .card .ava {
        width: 3rem !important;
        height: 3rem !important;
        margin: 0 !important;
        margin-right: 1em !important;
        border-radius: 4px;
        flex-shrink: 0; /* Prevents image from squishing */
    }

    .card .card-header {
        font-style: italic;
        overflow: hidden;
        width: 100%;
    }

    .card .card-header a {
        font-style: normal;
        color: #2bbc8a;
        font-weight: bold;
        text-decoration: none;
    }

    .card .card-header a:hover {
        color: #d480aa;
        text-decoration: none;
    }

    .card .card-header .info {
        font-style: normal;
        color: #a3a3a3;
        font-size: 14px;
        min-width: 0;
        overflow: hidden;
        text-overflow: ellipsis; /* Adds ... if text is too long */
        white-space: nowrap;
    }
</style>

<div class="links-content">
    <div class="link-navigation"></div>
</div>

<script>
    window.onload = () => {
        const linkNav = document.getElementsByClassName("link-navigation")[0];

        const html = links
            .map(
                (item) => `<div class="card">
    <img class="ava" src="${item.avatar}" />
    <div class="card-header">
        <div>
            <a href="${item.link}" target="_blank">${item.name}</a>
        </div>
        <div class="info">${item.desc}</div>
    </div>
</div>`,
            )
            .join("");

        linkNav.innerHTML = html;
    };
</script>
