[![Build Status](https://travis-ci.org/xexi/evens.svg?branch=master)](https://travis-ci.org/xexi/evens) &nbsp;<a href="https://snyk.io/test/github/xexi/evens"><img src="https://snyk.io/test/github/xexi/evens/badge.svg" alt="Known Vulnerabilities" data-canonical-src="https://snyk.io/test/github/xexi/evens" style="max-width:100%;"></a><br>
<img src="http://postfiles5.naver.net/MjAxNzAzMTVfMjMz/MDAxNDg5NTY4NjY0OTEw.NW1l-5VOppvl5pdxBfUnBJGv5bmnM7NM6sPoUr4fNQwg.c44oVld7u1gitW1YDpk-B9qGKrTKlkqSrfkjGpQyllYg.PNG.synth9/ev.PNG?type=w2"></img>
<br>
<br><b>Simple handling of sql queries in nodejs code</b>
<br><br>
<img src="http://postfiles16.naver.net/MjAxNzEwMTVfMTQ0/MDAxNTA4MDE5Nzg3NTUw.flwAWyTeSFo8dqq3rtpd_ubCECFDmnNNaB3174lvBB0g.LY5gvl-JfjaojhtsIWlvS3HRUnTjvIGE9seXarOoqaEg.JPEG.synth9/ori.JPG?type=w2"></img>
<b>⇩⇩⇩⇩⇩⇩⇩⇩ simplify ⇩⇩⇩⇩⇩⇩⇩⇩</b>
===================
<img src="http://postfiles13.naver.net/MjAxNzEwMTVfMjg4/MDAxNTA4MDE5ODMyOTE5.Rkwc3ftBAztDb1ZHPku9au6WLSnh6Yr_3foE1WiC4XAg.heV9EJpnWT5ic_kPC-EJ955uQWVjQs-7Dr5CJnzNTQgg.JPEG.synth9/ori2.JPG?type=w2"></img>
<br>
> *Note: * <b> evens takes care of of these things. </b>
>> <b>You don't need to worry about </b>:
>>> - **Connection management** (release or close)
>>> - **Commit**
>>> - **Rollback**
>>> - **Error handling**

<br>
#### <b>How to Use ? </b><br><br>
sample DB
<br>
<img src="http://postfiles12.naver.net/MjAxNzAzMjJfNSAg/MDAxNDkwMTUwODY4MTQ4.Z5KxDrrNyRgB42XJMAkGEPAT88DD8nWrhaHgWWQdNQsg.g9mqlU1JUriax-jNoBElUIfiyVRiXgVSz7V0uj9dmU4g.PNG.synth9/dd.PNG?type=w2"></img>

# simple query
```
"use strict";
let evens = require('evens').mysql;
let t = new evens({ host: '127.0.0.1', user: , password: , .... });

let plan = [ { query: 'SELECT * FROM gym'}]; // add more query by { query: 'blabla' }
t.query(plan);
t.on('end', (r)=>{ console.log(r.esult); });
```
<br><br>

# advanced query
```
"use strict";
let evens = require('evens').mysqlPool;
const mysql = require('mysql');
const pool = mysql.createPool({
  connectionLimit : 10,
  host     : '127.0.0.1', 
  user     : 'example',
  password : 'your_password',
  port     : 3306,
  database : 'your_db'
});

let plan = [ {
  query: { sql: 'UPDATE gym SET name = ? WHERE id = \'bird\'', timeout : 500000 },
  values: ['gym'],
  cData: { switch : 'off' } // must assign if you're going to use data chainning
}, {
  query: 'SELECT * FROM gym '
}, {
  customData : [ 1 ],
  customQuery : (data) => {
    // just give a number to customData you can retrive all plan[1] query result 
    if(data){
      return { query: 'SELECT * FROM gym '};
    } else {
      return 'pass'; /* must consider customData exception 
                        by 'pass' command you can just skip this query
                        or you can just quit process and end connection by 'end' command */
    }
  }
}, {
  // if query result not matched do not query
  customData : [ { 1: 'name'} ], // you can set static value or query result ( obj,str,arr )
  customQuery : (data, cData) =>{
    if(data[0]==='gym'){
      cData['switch'] = 'on'; // let's get more tricky
      return {
        query: 'SELECT * FROM ' + data[0],
        cData: cData 
      };
    } else {
      return 'pass'; 
    }
  }
}, {
  customQuery : (data, cData)=>{
    if(cData.switch==='on') return {query: 'SELECT * FROM day'}; // you can change program flow by cData   
  }
} ];

let Q = new evens(pool, {queryTimeout: 10000}); // set general query timeout
Q.query(plan);
Q.on('end', (r)=>{
  if(r.success){
    console.log(r.esult);    
  } else {
    console.log(JSON.stringify(r)); // err
  }
});
```
 
