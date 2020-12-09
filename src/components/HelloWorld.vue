<template>
  <div class="container">
    <div class="card text-center">
      <h1 class="card-title font-weight-bold mt-5 mb-5">
        Basil Classification
      </h1>
    </div>
    <div class="component card mb-3">
      <img
        :src="imageData"
        v-if="imageData.length > 0"
        class="card-img-top"
        alt="selected image"
      />
      <div class="card-body">
        <h5 class="card-title">
          Please select desire image that you want to predict. What type basil
          is it?
        </h5>
        <!-- <input type="file" @change="previewImage" accept="image/*" /> -->
        <button
          type="button"
          class="btn btn-outline-primary mt-3"
          @click="selectImage()"
        >
          Select Image
        </button>
      </div>

      <button
        id="myBtn"
        type="button"
        class="btn btn-success mt-3 mb-5 mx-5"
        v-if="imageData.length > 0"
        @click="predict()"
      >
        Predict
      </button>
    </div>
    <input
      type="file"
      class="hidden-input"
      id="input"
      @change="previewImage"
      accept="image/*"
    />

    <div
      class="alert alert-warning alert-dismissible fade show"
      role="alert"
      v-if="showAlert"
    >
      <strong>WARNNING!</strong> Please select image.
      <button
        type="button"
        class="close"
        data-dismiss="alert"
        aria-label="Close"
      >
        <span aria-hidden="true">&times;</span>
      </button>
    </div>

    <div id="app">
      <div v-if="showModal">
        <transition name="modal">
          <div class="modal-mask">
            <div class="modal-wrapper">
              <div class="modal-dialog modal-dialog-scrollable" role="document">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title">Prediction Result</h5>
                    <button
                      type="button"
                      class="close"
                      data-dismiss="modal"
                      aria-label="Close"
                    >
                      <span aria-hidden="true" @click="showModal = false"
                        >&times;</span
                      >
                    </button>
                  </div>
                  <div class="modal-body">
                    <h5 class="font-weight-bold">VGG model Result</h5>
                    <p class="">result = {{ result.vgg_result }}</p>
                    <p class="">
                      VGG holy prop = {{ result.vgg_holy_prop }}
                    </p>
                    <p class="">
                      VGG sweet prop = {{ result.vgg_sweet_prop }}
                    </p>
                    <p class="">
                      VGG lemon prop = {{ result.vgg_lemon_prop }}
                    </p>
                    <h5 class="font-weight-bold">
                      VGG with Finetune model Result
                    </h5>
                    <p class="">result = {{ result.vggfine_result }}</p>
                    <p class="">
                      VGG_Finetune holy prop =
                      {{ result.vggfine_holy_prop }}
                    </p>
                    <p class="">
                      VGG_Finetune sweet prop =
                      {{ result.vggfine_sweet_prop }}
                    </p>
                    <p class="">
                      VGG_Finetune lemon prop =
                      {{ result.vggfine_lemon_prop }}
                    </p>
                    <h5 class="font-weight-bold">Self-define model Result</h5>
                    <p class="">result = {{ result.lap_result }}</p>
                    <p class="">
                      Self-define holy prop =
                      {{ result.lap_holy_prop }}
                    </p>
                    <p class="">
                      Self-define sweet prop =
                      {{ result.lap_sweet_prop }}
                    </p>
                    <p class="">
                      Self-define lemon prop =
                      {{ result.lap_lemon_prop}}
                    </p>
                  </div>
                  <div class="modal-footer">
                    <button
                      type="button"
                      class="btn btn-primary"
                      @click="showModal = false"
                    >
                      Close
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      imageFile: "a",
      imageData: "",
      status: "",
      showAlert: false,
      showModal: false,
      result: {
        vgg_result: "",
        vgg_holy_prop: "",
        vgg_sweet_prop: "",
        vgg_lemon_prop: "",
        vggfine_result: "",
        vggfine_holy_prop: "",
        vggfine_sweet_prop: "",
        vggfine_lemon_prop: "",
        lap_result: "",
        lap_holy_prop: "",
        lap_sweet_prop: "",
        lap_lemon_prop: "",
      },
    };
  },
  methods: {
    selectImage: function() {
      var input = document.getElementById("input");
      input.click();
    },
    previewImage: function(event) {
      // Reference to the DOM input element
      var input = event.target;
      this.imageFile = input.files[0];
      // Ensure that you have a file before attempting to read it
      if (input.files && input.files[0]) {
        // create a new FileReader to read this image and convert to base64 format
        var reader = new FileReader();
        // Define a callback function to run, when FileReader finishes its job
        reader.onload = (e) => {
          // Note: arrow function used here, so that "this.imageData" refers to the imageData of Vue component
          // Read image as base64 and set to imageData
          this.imageData = e.target.result;
        };
        // Start the reader job - read file as a data url (base64 format)
        reader.readAsDataURL(input.files[0]);
      }
      // Check imageFile for show alert
      if (this.imageFile == "a"){
        this.showAlert = true;
      }else{
        this.showAlert = false;
      }
    },
    predict: function() {
        console.log("imageFile = " + this.imageFile);
        if (this.imageFile == "a") {
          this.showAlert = true;
          console.log(this.showAlert);
        }
        //this.showModal = true;
        //console.log(this.showModal);
        var bodyFormData = new FormData();
        bodyFormData.append("image", this.imageFile);

        axios({
          method: "post",
          url: "http://127.0.0.1:5000/predictions",
          data: bodyFormData,
          //headers: { "Access-Control-Allow-Origin": "*" },
        })
          .then((response) => {
            //handle success
            this.showModal = true;
            console.log(this.showModal);
            console.log(response.data)
            this.result = response.data.result
            console.log(this.result);
            console.log(response.data);
          })
          .catch((response) => {
            //handle error
            console.log(response);
          });


    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hidden-input {
  visibility: hidden;
}
.transparent {
  opacity: 0.5;
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
</style>
