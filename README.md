### [SteemFlag](https://steemflag.neocities.org/) A project to analyse Steemit downvote history
#### Aim of the project
[SteemFlag](https://steemflag.neocities.org/) is a website for analysing the downvote history of a Steemit user.
With this tool, the user can :
1. Analyse which users are downvoted by selected user
2. Analyse what is the total downvote power used, in total and exactly on which user
3. Analyse if the downvote power is mostly used on minnows, dolphins or whales
4. Discriminate if the user is flagging spams or is in a flag fight with some other user
#### Using the website
The website is coded in HTML and JavaScript and it is working at client side.
Not to mess with the asynchronous functions, the usage is set to be mostly manual.

* Goto [SteemFlag](https://steemflag.neocities.org/)  website.

* Main view
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519935219/wlf2aqlvebw9fssf2tp5.png)

* Enter the steemit name of the user you want to analyse
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519935299/rsp4lcdjipq3ivpq60ex.png)
* Press the " *Search Data* " button to start searching downvotes
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519935372/sukahkaqpum4zg7oizy4.png)
* Wait for the first part of data to be written on the page
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519935876/zntfyrfdsyv41tfovzbq.png)
* After first part of data is on the page, press " *Get Stats* " button to read the stats of the people downvoted.
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519936007/lhokge7ruepwmjshqmub.png)
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519936031/fb6pd3wdvcc1qrf3olh9.png)
This will show the flagged persons SP and define if he/she is a minnow, dolphin or whale.

* Taking this data, press the " *Calculate* " button
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519936138/xcsa8bvtbe7t1h2649la.png)

* This will calculate :
- How much downvote power is used on minnows
- How much downvote power is used on dolphins
- How much downvote power is used on whales

![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1519936236/tpyope7xhmop0fkz39hw.png)

#### How the project works and the code

The project is written in HTML and JavaScript using steem.js API.
It is an open-source project and the code can be found in GitHub: https://github.com/firedreamgames/steemflag

The code working is basically as follows :
First we have to get the steem data
> steem.api.getDynamicGlobalProperties(function(err, result) {
        document.getElementById("vesting_shares").innerHTML = result.total_vesting_shares;
        document.getElementById("vesting_fund").innerHTML = result.total_vesting_fund_steem;
      });
This is used to calculate the SP of the downvoter and the downvoted user.

Then we calculate the SP of the downvoter.

> steem.api.getAccounts([flagger], function(err, response) {
        console.log(response);
        vesting_shares = response[0].vesting_shares.split(' ')[0];
        delegated_vesting_shares = response[0].delegated_vesting_shares.split(' ')[0];
        received_vesting_shares = response[0].received_vesting_shares.split(' ')[0];
        total_vesting_shares = document.getElementById("vesting_shares").innerHTML;
        total_vesting_fund_steem = document.getElementById("vesting_fund").innerHTML;
        console.log(vesting_shares, delegated_vesting_shares, received_vesting_shares);
        var steem_power = steem.formatter.vestToSteem(vesting_shares, total_vesting_shares, total_vesting_fund_steem);
        var delegated_steem_power = steem.formatter.vestToSteem(received_vesting_shares - delegated_vesting_shares + ' VESTS', total_vesting_shares, total_vesting_fund_steem);
        document.getElementById("txt_fp").innerHTML = (steem_power + delegated_steem_power).toFixed(2);
      });

We get the flagged posts with getAccountHistory

> steem.api.getAccountHistory(flagger, -1, 10000, function(err, result) {
        console.log(result.length);
        for (let i = 0; i < result.length; i++) {
          if ((result[i][1].op[0] == 'vote') && (result[i][1].op[1].voter = flagger) && (result[i][1].op[1].weight < 0) && (result[i][1].op[1].author != flagger)) {
document.getElementById("vic_name").innerHTML = document.getElementById("vic_name").innerHTML + result[i][1].op[1].author + "<br />";
 document.getElementById("vic_SBD").innerHTML = document.getElementById("vic_SBD").innerHTML + (result[i][1].op[1].weight / 100) + " %" + "<br />";
          }
        }
      });

With a function similar to above one, we calculate the SP power of each downvoted user and determine if he/she is a minnow, dolphin or whale.

Then we read the data from the innerHTML ( this way function is sychronized ) and calculate the downvote totals for each group and write in the assigned div.


#### Roadmap
Flagging can be and is used for many reasons in Steemit.
* To punish spam post
* As an attack
* As a defense against attackers
It is important to have a handy tool to analyse users behaviours to use the downvote option.

The project is planned to be extended to:
* Work in a selected time slot
* Convert the % downvotes to SBD and see how much reward is downvoted.
* Cancel manual operation and use promises to handle asynchronous functions.

####  How to contribute?
The project needs contribution on
* Asynchronous function solution, so cancel 3 buttons to 1 calculate button
* Formulation and script to convert %downvoted to SBD stripped
* Timeslot selection to see daily, weekly or any time  performance of downvote.

Task requests will be opened on these 3 issues.


### New Features Added

#### 1. Async. function problem solved.
* BEFORE 

The project was working manually due to async function problem in Steem.js API.
3 buttons to be used simultaneously visualy confirming data is loaded to div.
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520432841/kyq5bhkyjtqfrw49gnmh.png)

* AFTER

Async. function problem solved with call to external function.
>var flagger = document.getElementById("input_flagger").value
      steem.api.getAccounts([flagger], function(err, response) {

The result is sent to an external function.
No promise or callback used.

>calc(result,flagger);
	 });

This way the manual operation is cancelled and the DIV is loaded automatically.
We have the complete RESULT array in CALC function to operate.

![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520433251/vqw42ueqf6wqrnlgkcsh.png)

Additionally, a "SPINNER" to the upper left corner is added to inform the user he has to wait.

#### 2. Date Selection added.

* BEFORE 

There was no date selection, the data was analysed as from the beginning.
Hence, there exists a limitation for 10 000 data.

* AFTER

![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520434207/hhptnqtij4xha3ubajex.png)

Date selector is added to the project.
With selected date, the data is seperated in FOR loop with IF  statement.

> for (let i = 0; i < result.length; i++) {
          if ((result[i][1].op[0] == 'vote') && (result[i][1].op[1].voter = flagger) && (result[i][1].op[1].weight < 0) && (result[i][1].op[1].author != flagger)&&(result[i][1].timestamp>selectedDate)) {

With above code, the downvotes "after the selected date" is filtered.

#### 3. Hyperlinks to the downvoted posts added.

* BEFORE

Hyperlinks of the downvoted posts didn't exist.
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520436810/qlryu7js2hyidozvcxnv.png)


* AFTER

Hyperlinks are added, so user can see the downvoted posts and comments.
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520434768/s7kc0zn5itpxi9sq0n0o.png)
>var str ="https://steemit.com/@"+result[i][1].op[1].author+"/"+result[i][1].op[1].permlink;
			var lin=(result[i][1].op[1].permlink).substring(0,70)+".......";


Hyperlinks are limited with 70 characters to fit the DIV.

#### 4. Analysis Modal window added.

* BEFORE

Analysis were limited. 

![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520435014/jm83t9yuablybmcdrowm.png)

* AFTER

Analysis is shown in a seperate modal window, full sorted from maximum times downvoted user to minimum.
![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520435173/hozt8w96ff5qqjkltdha.png)
The sorting code :

![image.png](https://res.cloudinary.com/hpiynhbhq/image/upload/v1520436176/to1usduj4v80wxamjbzc.png)

### Links
GitHub : https://github.com/firedreamgames/steemflag

WebPage : https://steemflag.neocities.org

