# mlSearchprototype
A search engine prototype for ecommerce product search. It uses naivebayes classifier and natural language processing with nodejs nlp library.


Technology stack:

*   naturalnode: https://github.com/NaturalNode/natural
   "Natural" is a general natural language facility for nodejs. Tokenizing, stemming, classification, phonetics, tf-idf, WordNet,       
    string similarity, and some inflections are currently supported.


LIVE DEMO: https://searchengine--sunnychouhan.repl.co/


var express = require('express');
var parser = require('body-parser');
var path = require('path');
var app = express();
var ejs = require('ejs');
var natural = require('natural');
var classifier = new natural.BayesClassifier();
var tokenizer = new natural.WordTokenizer();
var Trie = natural.Trie;
var trie = new Trie();
sw = require('stopword')

var opn = require('opn');

app.use(parser.urlencoded({ extended: false }))
app.use(parser.json())



//classifier training data

classifier.addDocument(['shirt','jeans'], 'clothing');
classifier.addDocument(['pant','skirt'], 'clothing');
classifier.addDocument(['women', 'men'], 'collection');
classifier.addDocument(['formal','casual'], 'attribute');
classifier.addDocument(['sport','party'], 'attribute');
classifier.addDocument(['nike','stop'], 'brand');
classifier.addDocument(['puma','lee'], 'brand');
classifier.addDocument(['blue','brown'], 'color');
classifier.addDocument(['white','black'], 'color');

classifier.train(); 

 

//trie database
var dlen = classifier.docs.length;

for(i=0;i<dlen;i++)
{

  for(j=0;j<4;j++)
  {
    var st = classifier.docs[i].text[j];
   if(typeof st === "undefined")
   {
   }
   else
   {
    trie.addString(st);

   }
   }

}


//




app.use(function(req,res,next){
    res.locals.userValue = null;
    next();
})
 
app.set('view engine','ejs');
app.set('views',path.join(__dirname))
 

//serving home page 
app.get('/',function(req,res){
    res.render('home',{
        topicHead : 'Search Engine Prototype',
    });
    console.log('user accessing Home page');
});



//getting query through post method
app.post('/',function(req,res){

 var tt = req.body.query;
 var obj = {};
 

//
// opens the url in the default browser 
opn('http://google.com/search?q='+tt);
  //tokenizer
  var to = tokenizer.tokenize(tt);
  const tok = sw.removeStopwords(to,['for']);
  

// Example Node.js program to append data to file
var fs = require('fs');
 
// append data to file
fs.appendFile('sample.txt',tok, 'utf8',
    // callback function
    function(err) { 
        if (err) throw err;
        // if no error
        console.log("Data is appended to file successfully.")
}); 



  console.log(tok);
    
    //rendering homepage with data
    res.render('home',{
        userValue : tok, 
        topicHead : 'Search Engine Prototype'
    });
});
app.listen(5000,function(){
    console.log('server running on port 5000');
})
