<template>
    <div class="container">
        <div class="form-group">
            <select v-model="selectedCountry" class="form-control" @change="getDataByCountry">
                <option value="">Global</option>
                <option v-for="country in countries" :value="country.Slug" :key="country.Slug">{{country.Country}}</option>
            </select>
        </div>
        <div>
            <div class="row justify-content-around">
                <div class="col-md-4">
                    <div class="stats-item btn-info">
                        <span class="item-title">Vaka</span>
                        <span class="item-count">{{confirmed.toLocaleString()}}</span>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stats-item btn-danger">
                        <span class="item-title">Ölüm</span>
                        <span class="item-count">{{deaths.toLocaleString()}}</span>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stats-item btn-success">
                        <span class="item-title">İyileşen</span>
                        <span class="item-count">{{recovered.toLocaleString()}}</span>
                    </div>
                </div>
            </div>
            <hr>
            <table class="table">
        <thead>
        <tr>
            <th scope="col">Sıra</th>
            <th scope="col">Ülke</th>
            <th scope="col">Vaka</th>
            <th scope="col">Ölüm</th>
            <th scope="col">İyileşen</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(item,index) in data.Countries" :key="index">
            <th scope="row">{{index+1}}</th>
            <td>{{item.Country}}</td>
            <td>{{item.TotalConfirmed.toLocaleString()}}</td>
            <td>{{item.TotalDeaths.toLocaleString()}}</td>
            <td>{{item.TotalRecovered.toLocaleString()}}</td>
        </tr>
        </tbody>
    </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
  data(){
    return {
      selectedCountry: '',
      countries: {},
      data: {},
      confirmed: 0,
      deaths: 0,
      recovered: 0
    }
  },
  // filters:{
  //   numberFormat(number){
  //     return number.toLocaleString();
  //   }
  // },
  methods:{
    getCountries(){
      axios.get("https://api.covid19api.com/countries")
      .then(response => this.countries = response.data);
    },
    getSummaryData(){
      axios.get("https://api.covid19api.com/summary")
      .then(response => {
        const data = response.data;
        this.data = data;
        this.confirmed = data.Global.TotalConfirmed;
        this.deaths = data.Global.TotalDeaths;
        this.recovered = data.Global.TotalRecovered;
      });
    },
    getDataByCountry(){
      let data = this.data.Countries.filter(country => {
        return country.Slug == this.selectedCountry;
      });
      data = data[0];
      this.confirmed = data.TotalConfirmed;
      this.deaths = data.TotalDeaths;
      this.recovered = data.TotalRecovered;
    }
  },
  mounted(){
    this.getCountries();
    this.getSummaryData();
  }
}
</script>


<style>
    body {
        background: antiquewhite;
    }
    table {
        background: #fff;
    }
    .container {
        margin-top: 40px;
    }
    .stats-item {
        padding: 20px;
        text-align: center;
        margin-bottom: 20px;
    }
    .item-title {
        display: block;
        color: #fff;
        font-size: 17px;
    }
    .item-count {
        font-weight: bold;
        font-size: 30px;
    }
</style>

