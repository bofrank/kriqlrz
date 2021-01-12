<template>
  <div>
    <button v-on:click="findSite">Get news summary.</button>
    <div>
      scrapings = {{ this.scrapings }}
    </div>
    <div>
        <text-analytics :initialCounter="initialCounter" :siteData="this.scrapings"></text-analytics>
    </div>
  </div>
</template>

<script>
    import TextAnalytics from './TextAnalytics.vue'
    const request = require('request');
    const cheerio = require('cheerio');
    export default {
        name: 'ScrapeSite',
        components: {
            TextAnalytics
        },
        data() {
            return {
                document: '',
                scrapings: '<div>Azure Cognitive Services Text Analysis</div>',
                initialCounter: 'this is also from the parent'
            }
        },
        methods: {
            findSite: function() {
                //var address = "http://www." + this.randomString(3, 'abcdefghijklmnopqrstuvwxyz') + ".com";
                //document.getElementById("randomURL").href = url;
                //document.getElementById("randomURL").innerHTML = url;
                //this.getSiteData(address);
                //this.getSiteData("http://lite.cnn.com/en");
                this.getSiteData("https://en.wikipedia.org/wiki/Portal:Current_events");
                //https://text.npr.org/
                //RSS
            },
            randomString: function(length, chars) {
                var result = '';
                for (var i = length; i > 0; --i) result += chars[Math.floor(Math.random() * chars.length)];
                return result;
            },
            getSiteData: function(address) {

                request({
                    method: 'GET',
                    //url: 'https://cors-anywhere.herokuapp.com/http://bofrank.com'
                    //TODO: investigate heroku fail
                    url: 'https://cors-anywhere.herokuapp.com/'+address
                }, (err, res, body) => {

                    if (err) alert(err);

                    let $ = cheerio.load(body);

                    let title = $('body');

                    //console.log(title.text());

                    this.scrapings = title.text().substr(0, 5000);

                    //this.evaluateScrapings(this.scrapings);


                });

            },
            evaluateScrapings: function(html){
                //if the site is useless then find a different site
                //TODO: put useless site in db to avoid next time
                if(html.length < 1000){
                    this.findSite();
                }
            }

        }
    }
</script>
