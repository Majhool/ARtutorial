# الجيل الرابع والخامس PC
| الدعم | الاصدار |
| :--- | :--- |
| بداية الدعم على الماك | OS X 10.8, Mountain Lion |

إذا كنت تريد طريقه انشاء الكونفق يدويا ولك معرفه باللغه الانجليزية [هنا ](https://dortania.github.io/OpenCore-Install-Guide/config.plist/haswell.html)رابط يشرح كيفيه انشاء الكونفق للجيل الرابع

**هذه الصفحة معدة على اساس انك تستخدم كونفقات هاكنتوش بالعربي**

???+ Warning "تنبية"
	قد تكون الصور احيانا غير محدثة او لاتظهر التعديلات الصحيحه
	**يرجى دائما اتباع الكلام المكتوب وتجاهل الصور** وظيفتها هي توضيح الاقسام لا غير الا لبعض الاستثانئات

## DeviceProperties

### Add

![](/img/config-setup/deviceproperties.png)

???+ info "PciRoot(0x0)/Pci(0x2,0x0)"
	هذا القسم مخصص لتحديد باتشات ال Framebuffer لكيكست [WhateverGreen](/EFI-setup/gathering-kexts#gpus)

	| AAPL,ig-platform-id | وصف |
	| :--- | :--- |
	| 0300220D | يستخدم لكروت انتل المدمجه في الجيل الرابع على ال PC في حاله استخدامها لعرض الشاشة |
	| 04001204 | يستخدم لكروت انتل المدمجه في الجيل الرابع على ال PC في حاله استخدامها لعمليات حسابيه فقط بدون عرض الشاشه |
	| 07002216 | يستخدم لكروت انتل المدمجة الخاصه بالجيل الخامس على ال PC |
	
	اذا كان لديك كرت HD 4400 فهو غير مدعوم من الماك بشكل رسمي لذلك ستحتاج تغيير اسمه, اضف الاتي للكونفق تحت `PciRoot(0x0)/Pci(0x2,0x0)` :
	
	| عنوان | نوع | القيمة |
	| :--- | :--- | :--- |
	| device-id | Data | 12040000 |
	
	ايضا اذا كنت تستخدم**الكرت المدمج لعرض الشاشه** ستحتاج الى اضافه اعدادت اضافيه :

	| عنوان  | النوع | القيمة | ملاحظة |
	| :--- | :--- | :--- | :--- |
	| AAPL,ig-platform-id | Data | قيمة الكرت الخاص بك | |
	| framebuffer-patch-enable | Data | 01000000 | |
	| framebuffer-stolenmem | Data | 00003001 | |
	| framebuffer-fbmem | Data | 00009000 | اذا لا يوجد خيار تحديد رام الكرت في البايوس |
	| device-id | Data | 12040000 |ضروري فقط ل HD4400 |
	
	**مثال** كرت HD4400 بدون وجود خيار للتحكم بحجم الرام في البايوس:
	
	![](/img/config-setup/4th-gen/HD4400-example.png)
## Kernel

### Quirks

![](/img/config-setup/kernel-quirks.png)

???+ info "Quirks"
	اعدادات مخصصه للكيرنل.
	
	| العنوان | مفعل | وصف |
	| :--- | :--- | :--- |
	| AppleCpuPmCfgLock | True | غير ضروري اذا تم تعطيل `CFG-Lock` في البايوس |
	| AppleXcpmCfgLock | True | غير ضروري اذا تم تعطيل `CFG-Lock` في البايوس |
	| DisableIOMapper | True | غير ضروري اذا تم تعطيل `VT-D` في البايوس |
	| LapicKernelPanic | False | اجهزه HP ستحتاج تفعيل هذا الاعداد |
	| PanicNoKextDump | True | |
	| PowerTimeoutKernelPanic | True | |
	| XhciPortLimit | True | |

## NVRAM

### Add

![](/img/config-setup/nvram-add.png)

???+ info "7C436110-AB2A-4BBB-A880-FE41995C9F82"

	هنا نقوم باضافه شروط اقلاع اضافيه مثلا لتحديد كروت الصوت وغيرها.
	 **اوامر اقلاع عامة:**

	| اوامر اقلاع  | وصف |
	| :--- | :--- |
	| **-v** |هذا احد اهم اوامر الاقلاع في عالم الهاكنتوش, يقوم بتفعيل وضع تفصيل حاله الاقلاع لاظهار جميع العمليات التي يقوم بها النظام. بدونه لايمكن معرفة سبب بانك بشكل صحيح فهو يظهر  معلومات مثلا حالة الاجهزه ايت مرحلة اقلاع فيها الجهاز وغيرها   |
	| **debug=0x100** | يقوم بتعطيل اعاده التشغيل في حالة حصول بانك في الاقلاع, لاعطائك فرصة لقرائة سبب توقف النظام **يستحسن استخدامة اثناء التثبيت وحل المشاكل** |
	| **keepsyms=1** | مرافق ل debug=0x100 بحيث يخبر النظام بطباعة علامات عند حصول بانك لاعطاء معلومات اكثر عن سبب حصول البانك بالاساس. |
	| **alcid=XX** | يستخدم لتحديد نوع كروت الصوت لتعريف [AppleALC.kext](/EFI-setup/gathering-kexts.md#sound) قائمة الكروت الصوت والرقم الخاص بها [من هنا](https://github.com/acidanthera/applealc/wiki/supported-codecs) **استخدم الارقام بعد كلمت layout** |

	* **اوامر اقلاع لكروت الشاشة**:

	| اوامر اقلاع | وصف |
	| :--- | :--- |
	| **agdpmod=pikera** | يستخدم لتعطيل board id على كروت navi(RX 5xxx) بدونه ستواجهه شاشة سوداء, لاتستخدمة على كروت من نوع اخر. |
	| **nvda_drv_vrl=1** | ضروري لتفعيل تعريفات انفيديا على فئة GTX 9xx و 10xx على سييرا وهاي سييرا |
	| **-wegnoegpu** | يستخدم لتعطيل جميع الكروت الخارجيه, مفيد بحاله اذا كان كرتك المنفصل غير مدعوم من النظام مثل كروت انفيديا الحديثة. |

## PlatformInfo

![](/img/config-setup/propertree-platforminfo.png)

هنا نقوم بوضع معلومات الجهاز مثل نوعه و السيريال ورقم اللوحة وغيرها, سنستخدم برنامج [GenSMBios](https://github.com/corpnewt/GenSMBIOS)

بعد تشغيل البرنامج اضغط رقم 1 لتنزيل الملفات الضرورية.

بعدها اضغط رقم 3 لتوليد معلومات الجهاز

بالنسبه للموديل الجهاز (SMBIOS) هناك عده اختيارات للجيل الرابع:

كاتلينا(macOS 10.15 Catalina) :

- `iMac14,1` اذا كنت ستسخدم الكرت مدمج فقط

- `iMac14,2` اذا كنت ستسخدم كرت منفصل

بيج سر (macOS 11 Big Sur) :

- `iMac14,4` اذا كنت ستسخدم الكرت مدمج فقط

- `iMac15,1` اذا كنت ستسخدم كرت منفصل

![](/img/config-setup/gensmbios.png)

يتم نقل الارقام بالشكل الاتي للكونفق:

- `Type` يتم نسخه الى Generic -> SystemProductName.
- `Serial` يتم نسخه الى  Generic -> SystemSerialNumber.
- `Board Serial` يتم نسخه الى  Generic -> MLB.
- `SmUUID` يتم نسخه الى  Generic -> Generic -> SystemUUID.

## UEFI

### Quirks

???+ info "اذا كان لديك جهاز HP"
	يجب عليك تفعيل UnblockFsConnect
	
	| العنوان | مفعل | وصف |
	| :--- | :--- | :--- |
	| UnblockFsConnect | True | ضروري لمذربوردات HP |