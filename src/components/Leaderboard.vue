<template>
  <div class="container">
    <h1 class="mt-4 text-center">Transport Guide</h1>
    <form @submit.prevent="calculateJourney">
      <div class="form-group">
        <label for="user">What is you startint point?</label>
        <input
          id="startPoint"
          name="startPoint"
          type="text"
          placeholder="Ex: Basel"
          v-model="startPoint"
          class="form-control"
          v-on:keypress="isLetter($event)"
          required
        />
        <label for="number">Where do you want to go?</label>

        <input
          id="endPoint"
          name="endPoint"
          type="text"
          placeholder="Ex: Zurich"
          v-model="endPoint"
          class="form-control"
          v-on:keypress="isLetter($event)"
          required
        />
      </div>
      <div>
        <label for="start">Date of journey: </label>
        <input
          type="date"
          id="start"
          name="dateOfJourney"
          v-model="dateOfJourney"
          min="2022-09-01"
          max="2032-12-31"
          required
        />
      </div>
      <br />
      <div>
        <button class="btn btn-dark">Calculate journey</button>
      </div>
    </form>
    <h3 class="mt-4 text-left">Journey timetable</h3>
    <table class="table mt-5">
      <thead>
        <tr>
          <th scope="col">Depart time</th>
          <th scope="col">Arrival time</th>
          <th scope="col">Journey</th>
          <th scope="col">Transfers</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(entry, key) in journey" :key="key">
          <td>{{ entry.departure }}</td>
          <td>{{ entry.arrival }}</td>
          <td>{{ entry.duration }}</td>
          <td>{{ entry.transfers }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'LeaderBoard',
  data() {
    return {
      journey: [],
    };
  },

  methods: {
    isLetter(e) {
      let char = String.fromCharCode(e.keyCode); // Get the character
      if (/^[A-Za-z]+$/.test(char)) return true;
      // Match with regex
      else e.preventDefault(); // If not match, don't add to input text
    },

    calculateJourney() {
      const from = this.startPoint;
      const whereto = this.endPoint;
      const dateOfJourney = this.dateOfJourney;

      let journey = null;
      fetch(
        `http://localhost:7070/v1/transportCalculation?from=${from}&to=${whereto}&date=${dateOfJourney}`
      )
        .then((response) => response.json())
        .then((response) => {
          console.log('drugi', response.payload);

          this.journey = response.payload;
          if (response.payload.length == 0) {
            alert('No transport found!');
          }
          return journey;
        })
        .catch((error) => {
          console.log('##########errpr', error);
          alert('Something went wrong! Try again later!');
        });
    },
  },
};
</script>

<style>
label {
  margin-right: 20px;
}
</style>