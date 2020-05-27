<template lang="pug">
  .container
    .row(v-if="!selectedImage")
      .col
        h5 Select an Image
    .row.border.p-2.rounded(v-else)
      .col-12
        .row
          .col-lg-8
            svg(height="400" width="100%")
              image(:href="selectedImage.url" height="400")            
              text(:x="string.x + 'em'" :y="string.y + 'em'"
                v-for="(string,index) in strings"
                :fill="strings[index].color"
                :font-size="string.size + 'em'") {{string.text}}
          .col-lg-4
            .row
              b-form-group(
              id="input-group-2"
              label="Text:"
              label-for="input-texts")
            .row(v-for="(string,index) in strings")
              .col-8
                .row
                  b-form-input(
                    id="input-2"
                    v-model="strings[index].text"
                    required
                    placeholder="Enter text")
                .row.py-2
                  .col-6
                    b-form-group(
                    id="input-group-2"
                    label="X"
                    label-for="input-texts")
                      b-form-input(
                        id="input-2"
                        v-model="strings[index].x"
                        type="number"
                        required
                        placeholder="Enter text")
                  .col-6
                    b-form-group(
                    id="input-group-2"
                    label="Y"
                    label-for="input-texts")
                      b-form-input(
                        id="input-2"
                        v-model="strings[index].y"
                        type="number"
                        required
                        placeholder="Enter text")
                .row.py-2
                  .col-4 Size
                  .col-8
                    b-form-input(
                      id="input-2"
                      v-model="strings[index].size"
                      type="number"
                      required
                      placeholder="Enter text")
              .col-4
                b-button(variant="info" @click="toggleShade(index)") Toggle Shade
            .row.m-1
              .col
                b-button(variant="success" @click="addString") Add Text
        .row.p-2
          .col-12
            b-button(variant="info" @click="dlCanvas") Generate
        .row.p-2(v-if="image")
          .col-12
            b-button(variant="success" :href="image" download="meme") Download
    .row
      .col-sm-6.col-md-4.col-lg-3.p-1(v-for="(image,index) in images" :key="image.id" @click="selectImage(index)")
        MemeImages(:image='image')
        
</template>

<script>
import MemeImages from "./MemeImages.vue";
import axios from "axios";
export default {
  components: {
    MemeImages
  },
  data() {
    return {
      image: null,
      images: [],
      selectedImage: null,
      strings: [{ text: "Hello World", color: "white", x: 1, y: 1 , size:1}]
    };
  },
  mounted() {
    axios
      .get("https://api.imgflip.com/get_memes")
      .then(response => (this.images = response.data.data.memes))
      .catch(function(error) {
        this.images = [];
        console.log(error);
      });
  },
  methods: {
    selectImage(index) {
      this.selectedImage = this.images[index];
    },
    addString() {
      this.strings.push({ text: "", color: "white", x: 1, y: 1 , size:1});
    },
    toggleShade(index) {
      this.strings[index].color === "white"
        ? (this.strings[index].color = "back")
        : (this.strings[index].color = "white");
    },
    dlCanvas() {
      var svg = document.querySelector("svg");

      // get svg data
      var xml = new XMLSerializer().serializeToString(svg);

      // make it base64
      var svg64 = btoa(xml);
      var b64Start = "data:image/svg+xml;base64,";

      // prepend a "header"
      var image64 = b64Start + svg64;

      // set it as the source of the img element
      this.image = image64;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
img {
  max-height: 400px;
  max-width: 400px;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
