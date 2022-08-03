<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to the Places App!",
      places: [],
      currentPlace: {},
      editPlaceParams: {},
      newPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/places").then((response) => {
        this.places = response.data;
        console.log("All places:", this.places);
      });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    createPlace: function () {
      axios
        .post("/places.json", this.newPlaceParams)
        .then((response) => {
          console.log("Sucess!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    updatePlace: function (place) {
      axios.patch("/places/" + place.id + ".json", this.editPlaceParams).then((response) => {
        console.log("Success!", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("/places/" + place.id).then((response) => {
        console.log("Success!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>New Place</h2>
    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
    </div>
    <button v-on:click="createPlace()">Create</button>
    <h2>All Places</h2>
    <div v-for="place in places" v-bind:key="place.id">
      <h4>{{ place.name }}</h4>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="editPlaceParams.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="editPlaceParams.address" />
        </p>
        <button v-on:click="updatePlace(editPlaceParams)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
