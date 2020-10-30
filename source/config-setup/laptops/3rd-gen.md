# جيل ثالث laptop
| الدعم | الاصدار |
| :--- | :--- |
| بداية الدعم على الماك | OS X 10.7, Lion |
| ملحوظة | كرت الشاشه المدمج لم يتم تاكيد دعمه في macOS 11(big sur) |

إذا كنت تريد طريقه انشاء الكونفق يدويا ولك معرفه باللغه الانجليزي [هنا ](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/ivy-bridge.html)رابط يشرح كيفيه انشاء الكونفق للجيل الثالث لللابتوبات

**هذه الصفحة معدة على اساس انك تستخدم كونفقات هاكنتوش بالعربي**

???+ Warning "تنبية"
	قد تكون الصور احيانا غير محدثة او لاتظهر التعديلات الصحيحه
	**يرجى دائما اتباع الكلام المكتوب وتجاهل الصور** وظيفتها هي توضيح الاقسام لا غير الا لبعض الاستثانئات

## DeviceProperties

### Add

![](/img/config-setup/3rd-gen/6-series-mobo.png)

???+ info "PciRoot(0x0)/Pci(0x16,0x0)"

	اذا كانت لديك مذربورد من الفئة السادسة(HM65,HM67) ستحتاج الى تغيير رقم جهاز ال IMEI حتى يكون مدعوم,
	قم باضافه الاتي تحت Add
	
	| Value | Type | Key |
	| :--- | :--- | :--- |
	| 3A1E0000 | Data | device-id |

![](/img/config-setup/deviceproperties.png)

???+ info "PciRoot(0x0)/Pci(0x2,0x0)"
	هذا القسم مخصص لتحديد باتشات ال Framebuffer لكيكست [WhateverGreen](/EFI-setup/gathering-kexts#gpus)

	| AAPL,ig-platform-id | وصف |
	| :--- | :--- |
	| 03006601 | يستخدم لشاشات بدقه 1366x768 او اقل |
	| 04006601 | يستخدم لشاشات بدقه 1600x900 او اكثر |
	| 09006601 | خيار ثالث في حاله عدم عمل الخيارات السابقه(اذا كانت تستخدم الشاشه `eDP`) |
	
	**ملاحظات:**
	
	- مداخل VGA لاتعمل ابدا اذا اشتغلت فهي بالحظ تقريبا.
	
	- اذا استخدمت خيار `04006601` فستحتاج اضافه خيارات جديده لتصليح مداخل الشاشه:

	| عنوان  | النوع | القيمة | ملاحظة |
	| :--- | :--- | :--- | :--- |
	| عنوان | TYPE | قيمه خاصه بك | ملاحظات |
	| framebuffer-patch-enable | Number | 1 | تفعيل باتشات |
	| framebuffer-memorycount | Number | 2 |  |
	| framebuffer-pipecount | Number | 2 |  |
	| framebuffer-portcount | Number | 4 | |
	| framebuffer-stolenmem | Data | 00000004 | وضع رامات الكرت 64 ميجا |
	| framebuffer-con1-enable | Number | 1 | سيسمح لنا بعمل باتشات(تعديلات) حتى نقوم بتشغل المداخل الخارجيه |
	| framebuffer-con1-alldata | Number | 4 | |
	| framebuffer-portcount | Data | 02050000 00040000 07040000 03040000 00040000 81000000 04060000 00040000 81000000 | تعريف المداخل |
	
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
	| **-wegnoegpu** | يستخدم لتعطيل جميع الكروت الخارجيه, مفيد بحاله اذا كان كرتك المنفصل غير مدعوم من النظام مثل كروت انفيديا الحديثة. **تمت اضافته سابقا**|

### Delete

**مخصص فقط للاجهزه التي لاتعمل فيها ال  ==NVRAM==**

- LegacyEnable: TRUE
	- يقوم بتخزين اعدادت ال nvram في ملف nvram.plist لمحاكاه دعم nvram
- LegacyOverwrite: TRUE
	- يسمح بتفعيل الاعدادات الموجوده في nvram.plist

## PlatformInfo

![](/img/config-setup/propertree-platforminfo.png)

هنا نقوم بوضع معلومات الجهاز مثل نوعه و السيريال ورقم اللوحة وغيرها, سنستخدم برنامج [GenSMBios](https://github.com/corpnewt/GenSMBIOS)

بعد تشغيل البرنامج اضغط رقم 1 لتنزيل الملفات الضرورية.

بعدها اضغط رقم 3 لتوليد معلومات الجهاز

بالنسبه للموديل الجهاز (SMBIOS) هناك عده اختيارات للجيل الثالث على الابتوبات:
igpu = كرت شاشه مدمج dgpu = كرت منفصل

| SMBIOS | نوع المعالج | نوع كرت الشاشة | حجم الشاشة |
| :--- | :--- | :--- | :--- |
| MacBookAir5,1 | ثنائي النواه 17w | iGPU: HD 4000 | 11" |
| MacBookAir5,2 | ثنائي النواه 17w | iGPU: HD 4000 | 13" |
| MacBookPro10,1 | رباعي النواه 45w | iGPU: HD 4000 + dGPU: GT650M | 15" |
| MacBookPro10,2 | ثنائي النواه 35w(فئه عليا) | iGPU: HD 4000 | 13" |

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