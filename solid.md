

<div dir="rtl">

 **- Tight Coupling** 
 -  occurs when modules are highly dependent on each   
   other changes in one module can have a significant impact on other   
   modules. difficult to modify, test, and maintain independently.
   
 **- Loose Coupling**
 -  occurs when modules have minimal dependencies on each 
   other.  can be modified, tested, and maintained independently without
   affecting other parts of the system.

	
## Single Responsiblty



A class should have only one responsibility and one reason to change
الـ Class بتاعتك المفترض يكون ليها مسؤولية واحدة بس، وسبب واحد بس هو اللي يخليك تفكر تعمل فيها Change

 بس ده مش معناه انك تعمل Class لكل Methods الموجوده
المقياس هو إن أياً كان عدد الـ Methods اللي جوه الـ Class (حتى لو كانوا 100 Method)، المهم إن كل دول يكونوا بيخدموا غرض واحد ومسؤولية واحدة وهدف واحد فقط.

كل ما الـ Class بتاعتك تكون ليها High Cohesion (تماسك عالي)، كل ما بتكون أفضل بكتير


## Open Closed
The software entities such as classes, modules, functions etc.
should be open for extensions but close for modifications

الـ OCP بيقولك إن الـ Software Entities (وده المقصود بيه الـ Classes أو الـ Modules) المفروض تكون:
1. Open for Extension " extends / impalements "
2. Closed for Modification (مقفولة للتعديل).
يعني إيه الكلام ده؟ يعني الـ Class بتاعتك بمجرد ما بتخلص وبتوصل لمرحلة إنها بتؤدي الـ Core Business بتاعتها بشكل ممتاز، المفروض تحطها في صندوق وتقفل عليها وماتلعبش فيها تاني خالص.

### طب لو جالنا Business Requirement جديد أو تعديل؟
الـ OCP بيقولك ما تعدلش في الـ Class نفسها. روح اعمل حاجة اسمها Extension (توسعة) وحط التعديل بتاعك في الـ Extension ده، أو زي ما بيسموه، ركب لها "ديل".
ده بنحققه باستخدام مبادئ الـ Object-Oriented زي الـ Inheritance / composition 


لو عندنا Class اسمها SalaryCalculator بتحسب المرتب لأنواع مختلفة من الموظفين. كل مرة نضيف نوع جديد زي                 ( Intern أو Freelancer)، كنا بنضطر نفتح الكود ونضيف شرط جديد (if condition). ده خالف مبدأ  (Open/Closed )          لأن الكلاس كانت مفتوحة للتعديل مع كل تغيير.  

الحل:  [apply [Abstraction + polymorphism 
خلينا الميثود abstract ، وبدل ما نعدل في الكلاس الأصلية، عملنا Classes فرعية لكل نوع موظف                                                  ( SalariedCalculator, InternCalculator, FreelancerCalculator) وكل واحدة بتحسب المرتب بطريقتها (polymorphism ) 
كده الكلاس الأصلية بقت مقفولة للتعديل لكن مفتوحة للإضافة عن طريق الوراثة (Extension).

النتيجة:
الكود بقى أكثر مرونة واستقرارًا.
كل نوع موظف له منطق حساب مستقل.
الحل قريب من Strategy Design Patterns
##  Liskov Substitution
Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program
لو عندك Base Class وعامل منها أكتر من Sub Type (أو عامل منها Inheritance في أكتر من Class تانية):
المفترض إنك في أي مكان بيستقبل أو بيتعامل مع الـ Base Class دي، تقدر تديله أي نسخة من الـ Sub Classes دي (اللي بتعمل منها Inheritance). والـ Code لازم يشتغل بدون ما يعمل مشاكل أو Unpredictable Behavior (تصرفات غير متوقعة).
ببساطة، المفروض تقدر تشيل واحدة من الـ Sub Classes وتحط التانية مكانها، والـ Application يفضل شغال بدون أي مشاكل.






## Interface Segregation

Clients should not be forced to depend upon interfaces that they do not use

الـ Clients (وهم أي Class أو أي حد خارجي هيستخدم أو هيعمل implement للـ Interface بتاعتك) مينفعش يتم إجبارهم إنهم يستعملوا Methods هما مش مهتمين بيها أو مش بيستخدموها.

لما بيكون عندك Interface كبيرة بتجمع Methods كتير، ده بيخلي أي Class بتعمل implement ليها مضطرة إنها تعمل override لـ Method معينة حتى لو هي مش بتنفذها. بنسمي ده fat interface

### الحل: الـ Segregation (فصل الـ Interfaces)
علشان تحقق الـ ISP، لازم تفصل الـ Interface بناءً على احتياج الـ Clients.
#### خطوات الحل:
1. الـ Interfaces الأصغر: بدل ما الـ Methods كلها تكون متجمعة في Interface واحدة كبيرة، بتكسرها لـ Interfaces أصغر. كل Interface بتبقى مسؤولة عن مجموعة features محددة.
2. الـ Client بيختار: الـ Class اللي بتعمل implement بتاخد بس الـ Interfaces اللي فيها الـ features اللي هي بتستخدمها. بما إنك تقدر تعمل implement لأكتر من Interface في Dart، فده بيحل المشكلة.

## Dependency Inversion
1. High-Level modules/classes should not depend on Iow-level modules/classes but both should depend on abstractions.
2. Abstractions should not depend on details but details(concrete implementations) should depend on abstractions.

   </div>
