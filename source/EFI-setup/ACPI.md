# ACPI

## ما هو ال ACPI؟

كما ذكرنا سابقا فملفات ال ACPI هي اشبه بخريطه الكمبيوتر للنظام, وبما ان الماك غير مدعوم على اجهزه الكمبيوتر بشكل رسمي سوف نحتاج الى التعديل على هذه الخريطه.
الملفات المبنية على هذا المعيار هي (Differentiated System Description Table)==DSDT== وهي الخريطة الاساسية التي تحتوي على الكنترولرات المدمجة  (embedded controllers) ساعه النظام و انوية المعالج وغيرها.

بينما ال  (**Secondary System Description Table**)  ==SSDT== يحتوي على معلومات ثانوية فقط,
ظيمكنك التفكير بان ==DSDT== هي خريطة المشروع بينما ==SSDT== هي ملاحظات اضافية.

## مفردات الجدول

- الجيل
	- جيل المعالج

- المعالج
	- اي SSDT يتعلق بالمعالج مثلا لتفعيل التحكم بالطاقه من النظام.

- كنترلورات مدمجة(Embedded controllers(==EC==))
	- جميع اجهزه انتل الحديثة تحتوي على ==EC== وحتى عده اجهزه AMD تظهره بملف ال DSDT لكن بشكل عام هذه الكنتلورات غير مدعومة بشكل رسمي من الماك, وقد تسبب بانك في الكيرنل. لذلك يجب اخفائها من النظام لكن من نظام كاتلينا فما فوق اصبح من الاجباري وجود جهاز ==EC== لذلك سنحتاج الى كتابه جهاز زائف.

	- المشكلة ان ال ==EC== في الابتوبات هو الذي يتحكم بالاختصارات و اظهار حالة البطارية لذلك يجب تفعيله,لكن تغيير اسم ال ==EC== قد يسبب بمشاكل في نظام ويندوز, لذلك انشاء EC زائف مع ابقاء الحقيقي فعال هو الحل الافضل
	
- ساعة AWAC للنظام
	- جميع مذربوردات فئة 300 (تشمل مذربودرات Z370) من انتل تاتي مع ساعة AWAC الجديدة مفعله بشكل افتراضي. وهذه تعتبر مشكله لان نظام الماك لحد الان لا يدعم التعامل معها, لذلك يجب علينا اجبار استخدام ساعات RTC القديمة او انشاء ساعه زائفة من اجل النظام	
	
- NVRAM 

	- ال ==NVRAM== هي مساحه تخزين داخل اجهزه للماك تسجل فيها اعدادات النظام المؤقته مثل سطوع الشاشه,علو الصوت,اعدادات الوقت وغيرها, وهي موجوده في الماك فقط وقد تحتاج الى تعديل بعض الاشياء اذا لم تعمل تلقائيا.

## Pc

| **الجيل** | **المعالج** | **EC** | **AWAC** | **NVRAM** | **USB** |
| :-------: | :-----: | :----: | :------: | :-------: | :-----: |
| الجيل الثالث (Ivy Bridge) | [CPU-PM](#cpu-pmaml) (بعد تثبيث النظام) | [SSDT-EC](#ssdt-ecaml) | - | - | - |
| الجيل الرابع(Haswell) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC](#ssdt-ecaml) | - | - | - |
| الجيل الخامس (Broadwell) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC](#ssdt-ecaml) | - | - | - |
| الجيل السادس (Skylake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | - | - | - |
| الجيل السابع (Kaby Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | - | - | - |
| الجيل الثامن/ التاسع (Coffee Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-AWAC](#ssdt-awacaml) | [SSDT-PMC](#ssdt-pmcaml) | - |
| الجيل العاشر (Comet Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-AWAC](#ssdt-awacaml) | - | [SSDT-RHUB](#ssdt-rhubaml) |
| AMD Ryzen (17h) | [SSDT-CPUR for B550](#ssdt-b550-cpuraml) | [SSDT-EC-USBX](#ssdt-usbxaml) | - | - | - |

- Backlight
	- SSDTs تستخدم لتصحيح التحكم بسطوع الشاشة في النظام.
	
- I2C Trackpad
	- SSDTs تستخدم لتفعيل تعريف VoodooI2C لتعريف تراك بادات تستخدم هذا البروتكول.
	
- IRQ 
	- يستخدم لتصحيح بعض التعارضات داخل ال DSDT, يستخدم للابتوبات بشكل عام.
	- اجهزه من الجيل السادس فما فوق من النادر ان تعاني من هذه المشكله, هذه الSSDT موجهه بشكل اكبر للجيل الخامس او اقدم.
	
## Laptop

**يرجى الانتباة لعمود IRQ اخر الجدول لاظهاره قم بسحب الجدول في الاسفل الى اليسار**

| **الجيل** | **المعالج** | **EC** | **Backlight** | **I2C Trackpad** | **AWAC** | **USB** | **IRQ** |
| :-------: | :-----: | :----: | :-----------: | :--------------: | :------: | :-----: | :-----: |
| الجيل الثالث (Ivy Bridge) | [CPU-PM](#cpu-pmaml) (بعد تثبيث النظام) | [SSDT-EC](#ssdt-ecaml) | [SSDT-PNLF](#ssdt-pnlfaml) | - | - | - | [SSDT-HPET](#ssdt-hpetaml) |
| الجيل الرابع(Haswell)  | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC](#ssdt-ecaml) | [SSDT-PNLF](#ssdt-pnlfaml)| [SSDT-GPI0](#ssdt-gpi0aml) | - | - | [SSDT-HPET](#ssdt-hpetaml) |
| الجيل الخامس (Broadwell)| [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC](#ssdt-ecaml) | [SSDT-PNLF](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | - | - | [SSDT-HPET](#ssdt-hpetaml) |
| الجيل السادس (Skylake)  | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | - | - | - |
| الجيل السابع (Kaby Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | - | - | - |
| الجيل الثامن (Coffee Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF-CFL](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | - | - | - |
| الجيل التاسع (Coffee Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF-CFL](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | [SSDT-AWAC](#ssdt-awacaml) | - | - |
| الجيل العاشر (Comet Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF-CFL](#ssdt-pnlfaml)| [SSDT-GPI0](#ssdt-gpi0aml) | [SSDT-AWAC](#ssdt-awacaml) | - | - |
| الجيل العاشر (Ice Lake) | [SSDT-PLUG](#ssdt-plugaml) | [SSDT-EC-USBX](#ssdt-usbxaml) | [SSDT-PNLF-CFL](#ssdt-pnlfaml) | [SSDT-GPI0](#ssdt-gpi0aml) | [SSDT-AWAC](#ssdt-awacaml) | [SSDT-RHUB](#ssdt-rhubaml) | - |

???+ Warning "تنبية"
	جميع ملفات ال SSDT التي تنتهي ب `aml.` او `dsl.` توضع في EFI/OC/ACPI


### CPU-PM.aml

???+ Warning "تنبية"
	هذا ال SSDT يتم عمله بعد تثبيت النظام.

- ماذا يعمل هذا ال SSDT؟

	هذا الSSDT يقوم بتفعيل التحكم بالطاقه على معالجات الجيل الثاني(**غير مدعومة**) و الجيل الثالث على الابتوبات.

سوف تحتاج الى سكربت [ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh) لعمل الملف

![](/img/EFI-setup/ACPI/ssdtPRGen.png)

بعد انتهاء عمل البرنامج ستجد ملف SSDT.aml موجود في `/Users/your-name>/Library/ssdtPRGen/ssdt.aml`

 يمكنك ايجاده بسهوله بختصار Cmd+Shift+G ثم لصق`~/Library/ssdtPRGen/`

بعد ذلك ننصحك بتغيير اسم الملف الى SSDT-pm.aml حتى تجده بشكل اسهل.


### SSDT-EC.aml

- ماذا يعمل هذا ال SSDT؟

	هذا ال SSDT يقوم بعمل  EC مزيف للاصدار كاتلينا(10.15+) فما فوق, لان النظام لا يعمل بدونها.

سوف تحتاج الى سكربت [SSDTTime](https://github.com/corpnewt/SSDTTime)

??? info "طريقه تحميل السكربت"
	تحمل السكربت بالضغط على زر Code ثم Download ZIP لتحميل السكربت كامل 
	![](/img/Github-zip.png)

![](/img/EFI-setup/ACPI/dumb-dsdt.png)

بعد فتح السكربت قم بختيار Dump DSDT (في هذه الحالة رقم 8) حتى ناخذ ال DSDT الاساسي.

بعد ذلك اختار رقم **2 اذا كان جهازك مكتبي و رقم 3 اذا كان جهازك لابتوب.**

بعد ذلك ستجد الSSDT موجود في ملف جديد اسمه Results موجود في موقع البرنامج على جهازك.

### SSDT-USBX.aml
- ماذا يعمل هذا ال SSDT؟

	هذا ال SSDT ضروري للجيل السادس فما فوق من معالجات انتل ومعالجات Ryzen.

**ستحتاج الى عمل [SSDT-EC.aml](#ssdt-ecaml) اولا**

بعد ذلك قم بتحميل (**قم بالضغط على زر download**) [SSDT-USBX.aml](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-USBX.aml) وضعه في ملف ال ACPI.

### SSDT-PLUG.aml

- ماذا يعمل هذا ال SSDT؟

	يقوم هذا الSSDT بتفعيل التحكم بطاقه المعالج من الماك للجيل الرابع فما فوق من معالجات انتل.

سوف تحتاج الى سكربت [SSDTTime](https://github.com/corpnewt/SSDTTime)

??? info "طريقه تحميل السكربت"
	تحمل السكربت بالضغط على زر Code ثم Download ZIP لتحميل السكربت كامل 
	![](/img/Github-zip.png)

![](/img/EFI-setup/ACPI/dumb-dsdt.png)

بعد فتح السكربت قم بختيار Dump DSDT (في هذه الحالة رقم 8) حتى ناخذ ال DSDT الاساسي.
ثم اختار رقم 4 

بعد ذلك ستجد `SSDT-PLUG.aml` موجود في ملف جديد اسمه Results موجود في موقع البرنامج على جهازك.

### SSDT-B550-CPUR.aml
- ماذا يعمل هذا ال SSDT؟

	هذا SSDT ضروري لكل مذربوردات B550 من AMD.

حمله من [هنا](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml)(**قم بالضغط على زر download**) ثم قم بوضعه بملف ACPI

### SSDT-AWAC.aml

- ماذا يعمل هذا ال SSDT؟

	يقوم بحل مشكله ساعه AWAC المزيد من التفاصيل [هنا](#_1)
??? info "قائمة المذربوردات التي تحتاج هذا ال SSDT"
	* B360
	* B365
	* H310
	* H370
	* Z370 (مذربوردات Gigabyte و Asrock مع بايوسات جديدة.)
	* Z390
	* B460
	* Z490
	* 400 series (الجيل العاشر, يشمل Z490)
	* 495 series (جيل عاشر نسخه 10nm(Comet lake))

سوف تحتاج الى سكربت [SSDTTime](https://github.com/corpnewt/SSDTTime)

??? info "طريقه تحميل السكربت"
	تحمل السكربت بالضغط على زر Code ثم Download ZIP لتحميل السكربت كامل 
	![](/img/Github-zip.png)

![](/img/EFI-setup/ACPI/dumb-dsdt.png)

بعد فتح السكربت قم بختيار Dump DSDT (في هذه الحالة رقم 8) حتى ناخذ ال DSDT الاساسي.
ثم اختار رقم 6 

بعد ذلك ستجد `SSDT-AWAC.aml` او `SSDT-RTC0.aml` على حسب جهازك, موجود في ملف جديد اسمه Results موجود داحل موقع البرنامج على جهازك.


### SSDT-PMC.aml
- ماذا يعمل هذا ال SSDT؟

	هذا ال SSDT يقوم بحل مشاكل NVRAM على مذربوردات فئه 300 من انتل (بستثناء Z370) 

سوف تحتاج الى سكربت [SSDTTime](https://github.com/corpnewt/SSDTTime)

??? info "طريقه تحميل السكربت"
	تحمل السكربت بالضغط على زر Code ثم Download ZIP لتحميل السكربت كامل 
	![](/img/Github-zip.png)

![](/img/EFI-setup/ACPI/dumb-dsdt.png)

بعد فتح السكربت قم بختيار  Dump DSDT (في هذه الحالة رقم 8) حتى ناخذ ال DSDT الاساسي.
ثم اختار رقم 5

بعد ذلك ستجد `SSDT-PMC.aml` موجود في ملف جديد اسمه Results موجود داخل موقع البرنامج على جهازك.

### SSDT-RHUB.aml

- ماذا يعمل هذا ال SSDT؟

	يقوم بحل مشكله بعض المذربوردات من فئه 400(asus و ASRock لاتعاني من هذه المشكله, msi غير مؤكد) كسرت قواعد ال ACPI وتسبب مشاكل في اقلاع الماك.


قم بتحميل (**قم بالضغط على زر download**) [SSDT-RHUB.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-RHUB.aml) وضعه في ملف ال ACPI.

### SSDT-PNLF.aml

- ماذا يعمل هذا ال SSDT؟

	يقوم بحل مشكله التحكم بسطوع شاشه على الابتوبات.

- [SSDT-PNLF.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF.aml)
	
	يعمل لكل الاجهزه تحت الجيل الثامن.

- [SSDT-PNLF-CFL.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF-CFL.aml)
	
	مخصص للجيل الثامن فما فوق.

### SSDT-GPI0.aml

- ماذا يعمل هذا ال SSDT؟
	
	يقوم هذا ال SSDT بتجهيز خريطه الجهاز حتى يتم تعريف التراك باد بستخدام [Voodoi2c](https://tutorial.هاكنتوش.com/EFI-setup/gathering-kexts/#_3)

حمله من [هنا](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-XOSI.aml)(**قم بالضغط على زر download**) ثم قم بوضعه بملف ACPI

بعد ذلك قم باضافه هذه الاعدادت على الكونفق في  config.plist -> ACPI -> Patch

| Comment | String | Change _OSI to XOSI |
| :--- | :--- | :--- |
| Enabled | Boolean | YES |
| Count | Number | 0 |
| Limit | Number | 0 |
| Find | Data | 5f4f5349 |
| Replace | Data | 584f5349 |

### SSDT-HPET.aml

- ماذا يعمل هذا ال SSDT؟
	
	يقوم هذا الSSDT بحل مشاكل تعارض IRQ في خرائط ال DSDT, وهو ضروري للابتوبات.

سوف تحتاج الى سكربت [SSDTTime](https://github.com/corpnewt/SSDTTime)

??? info "طريقه تحميل السكربت"
	تحمل السكربت بالضغط على زر Code ثم Download ZIP لتحميل السكربت كامل 
	![](/img/Github-zip.png)

![](/img/EFI-setup/ACPI/dumb-dsdt.png)

بعد فتح السكربت قم بختيار Dump DSDT (في هذه الحالة رقم 8) حتى ناخذ ال DSDT الاساسي.

بعد ذلك اختار رقم 1.

ثم عند ظهور هذه الشاشه **اضغط انتر**
![](/img/EFI-setup/ACPI/ssdt-hpet.png)

بعد ذلك ستجد الSSDT موجود في ملف جديد اسمه Results موجود في موقع البرنامج على جهازك.
