**Static NAT :**

**===========**



**en**

**conf**

**ip nat inside source static 192.168.1.5 10.0.0.5**

**ip nat inside source static 192.168.1.6 10.0.0.6**



**R1(config)# interface g0/0**

**R1(config-if)# ip nat inside**



**R1(config)# interface s0/0/0**

**R1(config-if)# ip nat outside**





**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**

**Dynamic NAT:**

**===========**

**enable**

**configure terminal**



**! LAN Interface**

**===============**



**interface fastEthernet0/0**

**ip address 192.168.1.1 255.255.255.0**

**ip nat inside**

**no shutdown**

**exit**



**! WAN Interface**

**==================**



**interface fastEthernet0/1**

**ip address 200.1.1.1 255.255.255.252**

**ip nat outside**

**no shutdown**

**exit**



**Small Scenario: Dynamic NAT with Pool and Access List**



**A company has an internal network 192.168.1.0/24 connected to a router.**

**Employees need to access the Internet, but their private IP addresses must be translated to public IP addresses.**



**The network administrator decides to:**



**Allow the internal network 192.168.1.0/24 to use NAT.**



**Create a public IP pool from 200.1.1.2 to 200.1.1.6.**



**Use an Access Control List (ACL) to specify which devices can use NAT.**



**Bind the ACL to the NAT pool so the router can translate the addresses.**



**CLI Configuration**

**enable**

**configure terminal**



**! Step 1: Define inside network using ACL**

**access-list 1 permit 192.168.1.0 0.0.0.255**



**! Step 2: Create NAT pool of public addresses**

**ip nat pool PUBLIC\_POOL 200.1.1.2 200.1.1.6 netmask 255.255.255.252**



**! Step 3: Bind ACL with NAT pool**

**ip nat inside source list 1 pool PUBLIC\_POOL**



**end**

**write memory**

**Result**



**When devices in 192.168.1.0/24 access the Internet:**



**Their private IPs are translated to public IPs from the pool.**



**Example:**



**192.168.1.10 → 200.1.1.2**



**192.168.1.20 → 200.1.1.3**



**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**



**write memory is a command used to save the current configuration of the router to permanent memory.**



**What it does**



**Routers have two main configuration areas:**



**Running Configuration (RAM) → the current active configuration.**



**Startup Configuration (NVRAM) → the configuration used when the router boots.**



**When you type:**



**write memory**



**the router copies the running configuration to the startup configuration.**



**So:**



**Running-config → Startup-config**

**Why it is important**



**If you do not save the configuration and the router:**



**restarts**



**loses power**



**then all changes will be lost.**



**Equivalent Command**



**write memory is the old shortcut for:**



**copy running-config startup-config**

**Example**



**After configuring NAT, DHCP, or interfaces:**



**end**

**write memory**



**This ensures the configuration remains after reboot. 💾**

**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**

**PAT:**

**====**



**enable**

**configure terminal**



**! LAN Interface**

**interface fastEthernet0/0**

**ip address 192.168.1.1 255.255.255.0**

**ip nat inside**

**no shutdown**

**exit**



**! WAN Interface**

**interface fastEthernet0/1**

**ip address 200.1.1.1 255.255.255.252**

**ip nat outside**

**no shutdown**

**exit**



**! Define ACL to match Inside LAN**

**access-list 1 permit 192.168.1.0 0.0.0.255**



**! Configure NAT Overload (PAT)**

**ip nat inside source list 1 interface fastEthernet0/1 overload**



**end**

**write memory**



**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**

**✅ الخلاصة:**



**Dynamic NAT → بتدي كل جهاز IP Public مختلف من Pool.**



**PAT / NAT Overload → كل الأجهزة تستخدم نفس الـ IP Public لكن بالـ Ports.**



**اللي انت كاتبه هو الـ PAT (الأشهر في البيوت والشركات الصغيرة).**

**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**















**الشرح:**



**ip nat inside → بتحدد إن الواجهة دي (Fa0/0) هي الداخلية (LAN).**



**ip nat outside → بتحدد إن الواجهة دي (Fa0/1) هي الخارجية (WAN/Internet).**



**access-list 1 permit 192.168.1.0 0.0.0.255 → السماح بترجمة كل الأجهزة في الشبكة الداخلية.**



**ip nat inside source list 1 interface fa0/1 overload → كل الأجهزة في LAN تطلع للإنترنت باستخدام IP واحد (200.1.1.1).**



**للتحقق:**

**show ip nat translations**

**show ip nat statistics**



**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**







**1. ip nat inside و ip nat outside**



**دول**

**labels**

**بتحطهم على**

**interfaces بتاعة الراوتر.**



**ip nat inside = LAN .**



**ip nat outside = WAN/Internet.**



**من غيرهم الراوتر مش هيعرف مين جوة ومين برة، ومش هيقدر يعمل Translation.**

**يعني دول مجرد تعريف الاتجاهات.**



**2. ip nat inside source static**



**ده بقى الترجمة نفسها.**



**IP inside local  192.168.1.10**

**IP inside global  200.1.1.10**



**فهو ده اللي بيقول للراوتر: "لما ييجي ترافيك من/إلى السيرفر ده، اعمله ترجمة كذا → كذا".**



**الفرق 👇**



**ip nat inside / outside = مجرد تعريف اتجاهات (LAN vs WAN).**



**ip nat inside source static ... = القاعدة اللي بتعمل الترجمة بين الـ IPs.**



**📌 مثال بسيط:**

**تخيل عندك شارع (الراوتر) وفيه بابين:**



**باب جوة البيت (LAN) → لازم تحط label "inside".**



**باب على الشارع (WAN) → لازم تحط label "outside".**



**بعد ما عملت كده، تيجي تقول:**

**"أي ضيف ييجي من بره على العنوان ده (200.1.1.10) دخله على الأوضة دي (192.168.1.10)" → وده هو الـ static NAT rule.**

**\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_**



**قصدي يا بطل الجهاز PC**

**جوه الشبكة الداخلية (LAN)، مش الراوتر 🚀**



**خليني أوضحلك:**



**🔹 الشكل:**



**\[PC: 192.168.1.10] --- (LAN) --- \[Router] --- (WAN/Internet)**





**الـ PC واخد IP داخلي (Private) زي 192.168.1.10.**



**الراوتر عنده:**



**واجهة LAN (Inside) → 192.168.1.1**



**واجهة WAN (Outside) → 200.1.1.1 (Public IP من مزود الخدمة).**



**🔹 Static NAT:**

**لو كتبت:**



**ip nat inside source static 192.168.1.10 200.1.1.10**





**أي حد من الإنترنت يطلب الـ IP 200.1.1.10 → الراوتر يوصّله على طول للـ PC (192.168.1.10).**



**والعكس، لو الـ PC خرج للإنترنت → هيبان دايمًا كـ 200.1.1.10.**



**📌 الراوتر نفسه مش هو اللي يتعمله NAT، هو مجرد "المترجم".**

**الـ NAT معمول عشان يخلي الأجهزة الداخلية (زي الـ PC) يبانوا بـ IP Public برة.**

