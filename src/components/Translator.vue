<template>
  <div class="translator">
    <input type="text" v-model="input"/>
    <button @click="startTranslate">Tulkot</button>
    <div id="output">output</div>
    <div id='test'></div>
  </div>
</template>

<script>
import * as emoji from "moji-translate"
export default {
  name: 'Translator',
  data: function() {
    return {
      input: 'vecīša cimdiņš'
      }
  },
  methods: {
    getTranslation(e) {
      let sourceText = ''
      if (e.parameter.q){
        sourceText = e.parameter.q;
      }
      let sourceLang = 'auto';
      if (e.parameter.source){
        sourceLang = e.parameter.source;
      }

      let targetLang = 'en';
      if (e.parameter.target){
        targetLang = e.parameter.target;
      }
      let url = "https://translate.googleapis.com/translate_a/single?client=gtx&sl=" + sourceLang + "&tl=" + targetLang + "&dt=t&q=" + encodeURI(sourceText);
  
      return fetch(url)
        .then(res => res.json())
        .then((out) => {
          let translatedText = out[0][0][0];
          return translatedText.trim();
        })
        .catch(err => { throw err });
    },
    prepareText(text) {
      return {
        parameter: {
          q: [text],
          target: 'en',
          source: 'lv'
        }
      }
    },
    startTranslate() {
      let toBeTranslated = this.input.split(' ')
      let outputArray = []
      let promises = []
      let testDiv = this.$el.querySelector('#test');
      testDiv.innerHTML = '';
      toBeTranslated.forEach(word => {
        promises.push(this.getTranslation(this.prepareText(word)).then(res => {
          let possibleEmoji = emoji.translateForDisplay(res)
          outputArray.push(possibleEmoji)
          let possibleEmoji2 = emoji.translate(res)
          testDiv.innerHTML += possibleEmoji2
        }))
      })
      Promise.all(promises).then(() => {
        let outputDiv = this.$el.querySelector('#output')
        outputDiv.innerHTML = '';
        outputArray.forEach(element => {
          outputDiv.appendChild(element)
        });
      })
    }
  },
  mounted: function () {
    this.startTranslate()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
