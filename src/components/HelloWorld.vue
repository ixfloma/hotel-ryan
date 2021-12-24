<template>
  <div class="row container">
    <div class="forms mx-3 my-4 col-lg-6">
      <form>
        <div class="my-3 mx-3">
          <label class="form-label">Kode Tamu</label>
          <select name="kodetamu" id="kodetamu" @change="kodeOnChange(kodetamu)" class="form-select" v-model.number="kodetamu">
            <option v-for="tamu in tamus" :value="tamu" :key="tamu.kode_tamu">
              {{ tamu.kode_tamu }}
            </option>
          </select>
        </div>
        <div class="my-3 mx-3">
          <label for="nama" class="form-label">Nama Tamu</label>
          <input type="text" name="nama" id="nama" class="form-control input-sm" placeholder="Nama Tamu" v-model="nama" disabled>
        </div>
        <div class="my-3 mx-3">
          <label for="nomorkamar" class="form-label">Nomor Kamar</label>
          <input type="text" name="nomorkamar" id="nomorkamar" placeholder="Nomor Kamar" v-model="nomorkamar" required class="form-control input-sm">
        </div>
        <div class="my-3 mx-3">
          <label for="jeniskamar" class="form-label">Jenis Kamar</label>
          <div class="row">
            <div class="col">
              <select id="jeniskamar" class="form-select" aria-label="jeniskamar" v-model.number="jeniskamar" @change="hargaUpdate">
                <option value="Single">Single</option>
                <option value="Double">Double</option>
                <option value="Suite">Suite</option>
                <option value="VIP">VIP</option>
              </select>  
            </div>
            <div class="col">
              <div class="input-group">
                <div class="input-group-text">Rp</div>
                <input type="text" class="form-control" v-model="harga" readonly>
                <div class="input-group-text">/Malam</div>
              </div>
            </div>
          </div>          
        </div>
        <div class="my-3 mx-3">
          <div class="row">
            <div class="col">
              <label for="tglmsk" class="form-label">Tanggal Inap</label>
              <input type="date" name="tglmsk" id="tglmsk" v-model="tglmsk" class="form-control">
            </div>
            <div class="col">
              <label for="tglklr" class="form-label">Tanggal Keluar</label>
              <input type="date" name="tglklr" id="tglklr" v-model="tglklr" class="form-control">
            </div>
          </div>
        </div>
        <div class="my-3 mx-3">
          <button class="btn btn-primary" @click="saveRekord">Tambahkan</button>
        </div>
      </form>
    </div>
  </div>
  <div class="row container">
    <div class="my-3 mx-3">
      <label class="form-label">Laporan Bulan</label>
      <select name="bulan" id="bulan" class="form-select" v-model.number="bulan" @change="getView">
        <option value="0">Semua</option>
        <option value="1">Januari</option>
        <option value="2">Februari</option>
        <option value="3">Maret</option>
        <option value="4">April</option>
        <option value="5">Mei</option>
        <option value="6">Juni</option>
        <option value="7">Juli</option>
        <option value="8">Agustus</option>
        <option value="9">September</option>
        <option value="10">Oktober</option>
        <option value="11">November</option>
        <option value="12">Desember</option>
      </select>
    </div>
    <div class="justify-content-center my-3 mx-3">
      <table class="table bordered-table">
        <thead>
          <tr>
            <th scope="col">No.</th>
            <th scope="col">Kode Tamu</th>
            <th scope="col">Nama Tamu</th>
            <th scope="col">Tanggal Inap</th>
            <th scope="col">Tanggal Keluar</th>
            <th scope="col">No Kamar</th>
            <th scope="col">Jenis Kamar</th>
            <th scope="col">Tarif/Malam</th>
            <th scope="col">Lama Inap</th>
            <th scope="col">Bayar Total</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="rekord in datasemua" :value="rekord" :key="rekord.kode_inap">
            <td>{{rekord.id_inap}}</td>
            <td>{{rekord.kode_tamu}}</td>
            <td>{{rekord.nama_tamu}}</td>
            <td>{{rekord.tgl_inap}}</td>
            <td>{{rekord.tgl_keluar}}</td>
            <td>{{rekord.no_kamar}}</td>
            <td>{{rekord.jenis_kamar}}</td>
            <td>Rp {{rekord.tarif}}</td>
            <td>{{rekord.durasi}} Hari</td>
            <td>Rp {{rekord.total}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
export default {
  data(){
    return {
      nama : '',
      nomorkamar: '',
      jeniskamar: 'Single',
      harga: '150000',
      kodetamu: '',
      datasemua : [],
      tamus : [],
      tglmsk: '',
      tglklr: '',
      bulan: '0'
    }
  },
  created() {
    this.getTamu();
    this.getView();
  },
  methods: {
    async saveRekord(){
      try {
        await axios.post("https://uas-backend-hotel.glitch.me/rekord", {
          kode_tamu: this.kodetamu.kode_tamu,
          tgl_inap: tglmsk.value,
          tgl_keluar: tglklr.value,
          no_kamar : this.nomorkamar,
          jenis_kamar : this.jeniskamar,
        });
      } catch(err) {
        console.log(err);
      }
    },
    async getView(){
      try {
        var response = null;
        if(this.bulan == 0){
          response = await axios.get("https://uas-backend-hotel.glitch.me/view");
        } else {
          response = await axios.get("https://uas-backend-hotel.glitch.me/view/partial?bulan="+this.bulan);
        }

        if(response.data.length == 0){
          this.datasemua = []
          console.log(this.datasemua)
        } else {
          for(var i = 0; i< response.data.length; i++){
            if(response.data[i].jenis_kamar == 'Single'){
              response.data[i].tarif = 150000
            } else if(response.data[i].jenis_kamar == 'Double'){
              response.data[i].tarif = 250000
            } else if(response.data[i].jenis_kamar == 'Suite'){
              response.data[i].tarif = 425000
            } else if(response.data[i].jenis_kamar == 'VIP'){
              response.data[i].tarif = 550000
            }
            response.data[i].tgl_inap = moment(response.data[i].tgl_inap).utc().format('YYYY-MM-DD')
            response.data[i].tgl_keluar = moment(response.data[i].tgl_keluar).utc().format('YYYY-MM-DD')
            const skrg = moment(response.data[i].tgl_inap)
            const nanti = moment (response.data[i].tgl_keluar)
            const durasi = moment.duration(nanti.diff(skrg));
            response.data[i].durasi = durasi.asDays();
            response.data[i].total = response.data[i].tarif * response.data[i].durasi;
            this.datasemua = response.data;
          }
        }
      } catch (err) {
        console.log(err)
      }
    },
    async getTamu(){
      try {
        const response = await axios.get("https://uas-backend-hotel.glitch.me/rekord");
        this.tamus = response.data;
        this.nama = this.tamus[0].nama_tamu;
        this.kodetamu = this.tamus[0];
        const hariini = new Date();
        const besok = new Date();
        besok.setDate(besok.getDate() + 1)
        this.tglmsk = moment(hariini).format('YYYY-MM-DD')
        this.tglklr = moment(besok).format('YYYY-MM-DD')
      } catch (err) {
        console.log(err);
      }
    },
    kodeOnChange(value){
      this.nama = value.nama_tamu
    },
    hargaUpdate(){
      if(this.jeniskamar == 'Single'){
        this.harga = '150000'
      } else if(this.jeniskamar == 'Double'){
        this.harga = '250000'
      } else if(this.jeniskamar == 'Suite'){
        this.harga = '425000'
      } else if(this.jeniskamar == 'VIP'){
        this.harga = '550000'
      }
    }
  }
    
}
</script>

<style>

</style>