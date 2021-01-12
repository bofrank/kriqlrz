<template>
  <div id="app">
    <div>
        <scrape-site></scrape-site>
    </div>
    <div>dataDisplay = {{dataDisplay}}</div>
  </div>
</template>

<script>
  import ScrapeSite from './components/ScrapeSite.vue'

const request = require('request');
const cheerio = require('cheerio');

export default {
  name: 'app',
  data: function () {
    return {
      dataDisplay: 'this variable is initialized',
      endpoint: 'https://artintelligence.cognitiveservices.azure.com/',
      path: '/text/analytics/v2.1/entities',
      subscription_key: '0000'
    }
  },
  components: {
    ScrapeSite
  },
  methods: {
    randomString: function(length, chars) {
        var result = '';
        for (var i = length; i > 0; --i) result += chars[Math.floor(Math.random() * chars.length)];
        return result;
    },
    getData: function(){
      request('https://cors-anywhere.herokuapp.com/http://bofrank.com', (error, response, html) => {
        if (!error && response.statusCode == 200) {
            const $ = cheerio.load(html);
            const siteHeading = $('body');
            let dataText = siteHeading.text();
            this.textAnalytics(dataText);
        }
      }
    )},
    textAnalytics: function(dataText){

      let scrapings = dataText;

      let documents = {
          'documents': [
              { 'id': '1', 'language': 'en', 'text': scrapings.substr(0, 5000) }
          ]
      };

      this.get_entities(documents);
    },
    get_entities: function(documents){
      let https = require('https');

      let body = JSON.stringify(documents);

      let request_params = {
          method: 'POST',
          hostname: (new URL(this.endpoint)).hostname,
          path: this.path,
          headers: {
              'Ocp-Apim-Subscription-Key': this.subscription_key,
          }
      };

      let req = https.request(request_params, this.response_handler);
      req.write(body);
      this.dataDisplay = req.end();
      alert("req.end()"+req.end());
    },
    response_handler: function(response){
      let body = '';
      response.on('data', function (d) {
          body += d;
      });
      response.on('end', function () {
          let body_ = JSON.parse(body);
          let body__ = JSON.stringify(body_, null, '  ');
          return body__;
      });
      response.on('error', function (e) {
          this.dataDisplay = 'Error: ' + e.message;
      });
    }
  },
  computed: {
    url: function() {
      return "http://www." + this.randomString(3, 'abcdefghijklmnopqrstuvwxyz') + ".com";
    }
  },
  mounted: function () {
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e51;
  margin: 50px;
}
</style>
