<template>
  <div>
    <div>
      initialCounter = {{ initialCounter }}
    </div>
    <button v-on:click="count++">You clicked me {{ count }} times.</button>
    <br />
    <!--
    <button v-on:click="getData">Get Data</button>
    -->
    <div>
      Data = {{ document }}
    </div>
    <div>
      Results = {{ results }}
      <br />
      <br />
      <div v-if="results">
        <div v-for="entity in results[0].entities" :key="entity">

            type = {{ entity.type }} -------------------- name={{ entity.name }}

        </div>
        <div>ArtIntelligence has generated the artwork title "{{ title }}"</div>
      </div>
    </div>
  </div>
</template>

<script>

  const count = 0;
  export default {
    name: 'TextAnalytics',
    props: ['initialCounter','siteData'],
    data() {
      return {
        heroes: [],
        count: count,
        document: '',
        results: null,
        counter: this.initialCounter,
        scrapings: this.siteData,
        title: ''
      }
    },
    methods: {
      getData: function() {
        let https = require('https');
        const subscription_key = "0000";
        const endpoint = "https://artintelligence.cognitiveservices.azure.com/";
        let path = '/text/analytics/v2.1/entities';
        this.count = 100;
        //let scrapings = '<div>Test Azure Cognitive Services Text Analytics for Software Development and other stuff</div>';

        this.document = {
            'documents': [
                { 'id': '1', 'language': 'en', 'text': this.siteData }
            ]
        };

        let body = JSON.stringify(this.document);

        let request_params = {
            method: 'POST',
            hostname: (new URL(endpoint)).hostname,
            path: path,
            headers: {
                'Ocp-Apim-Subscription-Key': subscription_key,
            }
        };

        let req = https.request(request_params, this.response_handler);
        req.write(body);
        req.end();

      },
      response_handler: function(response) {
          let body = '';
          response.on('data', function (d) {
              body += d;
          });
          response.on('end', function () {
              let body_ = JSON.parse(body);
              callback(null,body_);
          });
          response.on('error', function (e) {
              alert('Error on getting data: ' + e.message);
          });
          let callback = (whatever,body) => {
            this.results = body.documents;
            this.constructTitle();
          };
      },
      constructTitle: function(){

          //TODO:
          //put key and endpoint in .env
          //use bing image search
          //get image (prioritize figures) from drawing
          //get vector file images.google.com
          // for example "3 March, Cambridgeshire svg"
          //process in blender with Python
          //post to cgtrader https://www.cgtrader.com/profile/upload/model
          //get stats https://www.cgtrader.com/profile/analytics
          //runway ML
          //Azure RL
          //Azure AIoT
          //Azure Computer Vision
          //Azure Video Indexer
          //scheduler from azure cog services

          let tempArray = [];
          let personIdentified = false;
          let locationIdentified = false;
          let dateTimeIdentified = false;

          for(let i = 0;i<((this.results[0].entities.length));i++){

            if((this.results[0].entities[i].type === 'Person') && (!personIdentified)){
              personIdentified = true;
              if(tempArray.length > 0){
                tempArray.unshift(this.results[0].entities[i].name + ' in ');
              } else {
                tempArray.unshift(this.results[0].entities[i].name+' ');
              }
            } else if((this.results[0].entities[i].type === 'Location') && (!locationIdentified)){
              locationIdentified = true;
              if(tempArray.length > 0){
                tempArray.splice(1, 0, this.results[0].entities[i].name);
              } else {
                tempArray.unshift(this.results[0].entities[i].name);
              }
            } else if((this.results[0].entities[i].type === 'DateTime') && (!dateTimeIdentified)){
              dateTimeIdentified = true;
              tempArray.push(' '+this.results[0].entities[i].name+' ');
            } else if ((tempArray.length == 0)&&(this.results[0].entities.length == 1)){
                tempArray.push(this.results[0].entities[i].name);
            } 

          }

          if(tempArray.length>2){
            this.title = tempArray[0]+tempArray[1]+tempArray[2];
          } else if(tempArray.length == 2){
            this.title = tempArray[0]+tempArray[1];
          } else {
            this.title = tempArray[0];
          }

          //find svg
          // https://www.bing.com/images/search?sp=-1&pq=apple+svg&sc=1-9&cvid=830DC44F6EA84BB2A75BDCF35DD52150&tsc=ImageBasicHover&cw=1351&ch=581&q=apple+svg&qft=+filterui:photo-linedrawing&form=IRFLTR&first=1
          // https://www.bing.com/images/search?&q=apple+svg&qft=+filterui:photo-linedrawing
          //window.location = "https://www.bing.com/images/search?&q="+this.title+"+svg&qft=+filterui:photo-linedrawing";

          // https://www.google.com/search?q=apple%20svg&tbm=isch&tbs=itp:lineart&hl=en&sa=X&ved=0CAIQpwVqFwoTCLDG5qeOwO0CFQAAAAAdAAAAABAC&biw=1351&bih=619

          window.location = "https://www.google.com/search?q="+this.title+"%20svg&tbm=isch&tbs=itp:lineart";

          //https://www.google.com/search?as_st=y&tbm=isch&hl=en&as_q=&as_epq=star&as_oq=&as_eq=&cr=&as_sitesearch=&safe=images&tbs=itp:lineart,ift:svg

          //build Programmable Search Engine
          //https://developers.google.com/custom-search/v1/overview
          //https://programmablesearchengine.google.com/cse/search/advanced?cx=e2aee65249e87b8b5

          //bing search
          //portal.azure.com
          //https://docs.microsoft.com/en-us/bing/search-apis/bing-image-search/tutorial/bing-image-search-single-page-app

      }
    }
  }
</script>
