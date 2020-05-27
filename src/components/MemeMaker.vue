<template lang="pug">
  .container
    .row(v-if="!selectedImage")
      .col
        h5 Select an Image
    .row.bg-success.p-2.rounded(v-else)
      .col-12
        .row
          .col
            div(contenteditable="false")
              svg(height="400"  length="auto")
                image(:href="selectedImage.url" height="400")            
                text(:x="string.x + 'em'" :y="string.y + 'em'"
                  v-for="(string,index) in strings"
                  :fill="strings[index].color"
                  font-size="1em") {{string.text}}
          .col
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
              .col-4
                b-button(variant="info" @click="toggleShade(index)") Toggle Shade
            .row.m-1
              .col
                b-button(variant="success" @click="addString") Add Text
        .row.p-2(v-if="image")
          .col-6
            | IMAGE
            img(:src="image") 
          .col-6
            | CANVAS
            canvas
        .row.p-2
          .col-12
            b-button(variant="success" @click="dlCanvas") Download
    .row
      .col-3-md.p-1(v-for="(image,index) in images" :key="image.id" @click="selectImage(index)")
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
      strings: [{ text: "Hello World", color: "white", x: 1, y: 1 }]
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
      console.log(this.images[index]);
      this.selectedImage = this.images[index];
    },
    addString() {
      this.strings.push({ text: "", color: "white", x: 1, y: 1 });
    },
    toggleShade(index) {
      this.strings[index].color === "white"
        ? (this.strings[index].color = "back")
        : (this.strings[index].color = "white");
    },
    dlCanvas() {
      var svg = document.querySelector("svg");
      var img = document.querySelector("img");
      var canvas = document.querySelector("canvas");

      // get svg data
      var xml = new XMLSerializer().serializeToString(svg);

      // make it base64
      var svg64 = btoa(xml);
      var b64Start = "data:image/svg+xml;base64,";

      // prepend a "header"
      var image64 = b64Start + svg64;

      // set it as the source of the img element
      setTimeout(() => this.image = image64, 2000);
      
      console.log("image64", image64)

      // draw the image onto the canvas
      canvas.getContext("2d").drawImage(img, 0, 0);

      var dt = canvas.toDataURL("image/png"); // << this fails in IE/Edge...
      dt = dt.replace(/^data:image\/[^;]*/, "data:application/octet-stream");
      dt = dt.replace(
        /^data:application\/octet-stream/,
        "data:application/octet-stream;headers=Content-Disposition%3A%20attachment%3B%20filename=Canvas.png"
      );
      this.href = dt;
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
