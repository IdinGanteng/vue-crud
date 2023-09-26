<template>
    <div class="container">
      <div class="row">
        <div class="col-sm-6 mt-3">
          <div class="card">
            <div class="card-body card-bar">
              <Bar v-if="loaded" :data="barChartData" />
            </div>
          </div>
        </div>
        <div class="col-sm-6 mt-3">
          <div class="card d-flex">
            <div class="card-body mx-auto card-pie">
              <Pie v-if="loaded" :data="pieChartData"/>
            </div>
          </div>
        </div>
        <div class="col-sm-6 mt-3">
          <div class="card">
            <div class="card-body">
              <Line v-if="loaded" :data="lineChartData" />
            </div>
          </div>
        </div>
        <div class="col-sm-6 mt-3">
          <div class="card">
            <div class="card-body card-bar">
              <Bar v-if="loaded" :data="barChartData" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    import { Bar, Pie, Line } from 'vue-chartjs'
    import { 
      Chart as ChartJS,
      Title,
      Tooltip,
      Legend,
      LineElement, 
      ArcElement, 
      BarElement, 
      CategoryScale, 
      LinearScale,
      PointElement } from 'chart.js'

    ChartJS.register(Title, Tooltip, Legend, BarElement, LineElement, PointElement, ArcElement, CategoryScale, LinearScale)

  
  export default {
    name: 'BarAndPieAndLineChart',
    components: { Bar, Pie, Line},
    data: () => ({
      loaded: false,
      barChartData: null,
      pieChartData:null,
      lineChartData:null,
      
    }),
    async mounted () {
      this.loaded = false
  
      try {
        const response = await fetch('http://localhost:8000/user') // Mengambil data dari API
        const data = await response.json() // Menguraikan respons sebagai JSON
  
        // Menentukan label bulan secara manual
        const labels = [
          'Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni',
          'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'
        ]
  
        // Inisialisasi objek untuk menyimpan jumlah catatan lunas dan hutang untuk setiap bulan
        const monthlyData = {};
        labels.forEach((bulan) => {
          monthlyData[bulan] = { lunas: 0, hutang: 0 };
        })
  
        // Menghitung jumlah catatan "lunas" dan "hutang" untuk setiap bulan
        data.forEach((user) => {
          if (user.catatan && user.bulan) {
            const bulan = user.bulan.charAt(0).toUpperCase() + user.bulan.slice(1).toLowerCase(); // Konversi bulan menjadi format yang sesuai
            if (labels.includes(bulan)) { // Periksa apakah bulan termasuk dalam label yang ditentukan
              if (!monthlyData[bulan]) {
                monthlyData[bulan] = { lunas: 0, hutang: 0 };
              }
              if (user.catatan === 'lunas') {
                monthlyData[bulan].lunas += 1;
              } else if (user.catatan === 'hutang') {
                monthlyData[bulan].hutang += 1;
              }
            }
          }
        })
  
        // Disesuaikan dengan format data yang sesuai dengan grafik batang
        this.barChartData = {
          labels: labels, // Menggunakan label yang telah ditentukan
          datasets: [
            {
              label: 'Lunas', // Label dataset untuk catatan lunas
              backgroundColor: 'blue', // Warna latar belakang batang
              data: labels.map((bulan) => monthlyData[bulan].lunas) // Jumlah catatan lunas untuk setiap bulan
            },
            {
              label: 'Hutang', // Label dataset untuk catatan hutang
              backgroundColor: 'red', // Warna latar belakang batang
              data: labels.map((bulan) => monthlyData[bulan].hutang) // Jumlah catatan hutang untuk setiap bulan
            }
          ]
        }
        const totalLunas = labels.map((bulan) => monthlyData[bulan].lunas).reduce((a, b) => a + b, 0);
        const totalHutang = labels.map((bulan) => monthlyData[bulan].hutang).reduce((a, b) => a + b, 0);

        const pieData = {
            labels: ['Lunas', 'Hutang'],
            datasets: [
            {
                data: [totalLunas, totalHutang], // Jumlah catatan lunas dan hutang
                backgroundColor: ['blue', 'red'] // Warna latar belakang sektor pie
            }
            ]
        }
        this.lineChartData = {
          labels: labels, // Label bulan
          datasets: [
            {
              label: 'Lunas',
              borderColor: 'blue', // Warna garis
              backgroundColor: 'transparent', // Warna latar belakang
              data: labels.map((bulan) => monthlyData[bulan].lunas) // Data untuk grafik garis
            },
            {
              label: 'Hutang',
              borderColor: 'red', // Warna garis
              backgroundColor: 'transparent', // Warna latar belakang
              data: labels.map((bulan) => monthlyData[bulan].hutang) // Data untuk grafik garis
            }
          ]
        };
        
        this.pieChartData = pieData
  
        this.loaded = true
      } catch (e) {
        console.error(e)
      }
    }
  }
  </script>
  <style>
  .card-pie{
    width: 280px;
    height: auto;
  }
  .card-bar{
    width: 102%;
    height: auto;
  }
  .card{
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
    transition: 0.3s;
  }
  .card:hover {
    box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
  } 
</style>