<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h3>{{ message2 }}</h3>
    <h2>Take a Look at these PLACES tho!</h2>
    <button v-on:click="createPlace()">Add a new place</button>
    <div id="inputs">
      <input type="text" v-model="newPlaceName" placeholder="Name" />
      <input type="text" v-model="newPlaceAddress" placeholder="Address" />
    </div>

    <br />

    <div class="container">
      <div id="index" v-for="place in places" v-bind:key="place.id">
        <h3>
          {{ place.name }}
          <br />
          <button v-on:click="showPlace(place)">Show Info</button>
          <br />
        </h3>
      </div>
    </div>
    <dialog id="place-info">
      <form method="dialog">
        <h3>{{ currentPlace.name }}</h3>
        <p>{{ currentPlace.address }}</p>
        <input type="text" v-model="currentPlace.name" placeholder="name" />
        <br />
        <input type="text" v-model="currentPlace.address" placeholder="address" />
        <br />
        <button v-on:click="updatePlace(currentPlace)">Update Info</button>
        <br />
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>close me</button>
      </form>
    </dialog>

    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
  </div>
</template>

<style>
.home {
  background-color: #fff5cc;
}

.container {
  display: inline-flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
}

.container > div {
  margin-left: 2%;
  margin-right: 2%;
}

.container button {
  color: white;
  text-shadow: 1px 1px 1px black;
  background-color: #ffcc00;
  border: none;
  box-shadow: 1px 1px 7px black;
}

h1 {
  font-size: 3em;
  color: orange;
  text-shadow: 1px 2px 4px black;
  margin-bottom: -3%;
}

h3 {
  font-size: 1.5em;
  color: #ffcc00;
  text-shadow: 1px 1px 2px black, 1px 1px 1px black;
}

p {
  color: #ffcc00;
  text-shadow: 1px 1px 7px black, 1px 1px 1px black;
}
</style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "PLACES!",
      message2: "... it sure is an app!",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      console.log("creating another place");
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("api/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-info").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log("updated!", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios.delete("/api/places/" + place.id).then((response) => {
        console.log("destroyed!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
