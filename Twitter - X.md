مساء الخير , اول writeup هننزلها
المقاله من **Dhananjay Pathak** الباحث الامني وبيحكي عن ازاي اكتشف ثغره في احد المواقع **قدر من خلالها يغير الايميل بتاع الاكونت من غير verify وممكن تؤدي ل takeover علي الاكونت**
###### 1. Subdomain Enumeration
- اول حاجه عملها Dhan كا عادة اي hunter وعمل subdomain enumeration
- استخدم فالخطوه دي اداة ال **Sublist3r** وال **Subfinder** وال **Amass**
```
sublist3r -d target.com -v -o ~/target/sublister_subdomains.txt
subfinder -d target.com -o ~/target/subfinder_subdomains.txt
amass enum -passive -norecursive -noalts -d target.com -o ~/target/amass_subdomains.txt
```
###### 2. Merging and Sorting Subdomains
- وعشان يتجنب ال subdomains المتكرره بدأ انه يدمجهم مع بعض ويرتبهم كويس
###### 3. Checking for Alive Subdomains
- بعد ماجمع شوية subdomains بدأ بقي انه يشوف مين اللي شغال ومين اللي مش شغال
- واستخدم فالخطوه دي اداة ال **HTTPX**
```
httpx -l ~/target/all_subdomains.txt -o ~/target/live_subdomains.txt
```
###### 4. Identifying Subdomains with Registration and Login Functionalities
- هو طبعا Dhan داخل عشان يدور علي الثغره دي بالذات فا هو بدأ يشوف ال subdomains اللي ليها علاقه بال Registration وال Login لأني دي الحاجه اللي هيكون ليها علاقه بال Authentication Bypass او Account Takeover او IDOR
###### The Discovery Moment
- المهم بقي Dhan جرب يعمل اكونت بايميل معين وجرب انه يغيره
- و زي اي موقع لينك طلب التغيير اتبعت للايميل عشان المفروض لما يدوس عليه يقولو بقي دخل الايميل الجديد بس Dhan لاحظ حاجه غريبه فاللينك اللي اتبعت للايميل
```
https://app.target.com/email-link/weblogin/v7/account/newemail?email=████████&token=██████████
```
لاحظت حاجه؟
- في هنا اتنين params اول واحد وده ال email ودا بيكون فيه الايميل الجديد اللي انت هتغير ليه وال token ده ال token بتاع الاكونت وده اهم حاجه لأني لو الtoken اتغير الثغره مش هتبقي ثغره كده
- بدأ بقي انه يستخدم ال Burp Suite وعمل intercept وعدل علي اللينك ده اللي رايح للسيرفر وحط اي ايميل هو عاوزه وفعلا السيرفر قبل ال request من غير اي عملية validation وده ممكن يخلي اي حد ياخد Takeover علي اي اكونت فالموقع ده
**وبس كده Dhan رفع report للموقع علي HackerOne وقبلوها واتصنفت High**
- لينك المقال : https://medium.com/@dhananjay_00/how-i-discovered-an-email-change-vulnerability-leading-to-pre-account-takeover-p2-7e76e0002eef
- التويتر بتاع Dhan : https://x.com/dhananjay_00
