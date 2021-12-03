<template>
  <div class="food-detail">
    <Navbar />
    <div class="container">
      <!-- breadcrumb -->
      <div class="row mt-4">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">
                Food Order
              </li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row mt-3">
        <div class="col-md-6">
          <img
            :src="'../assets/images/' + product.gambar"
            class="img-fluid shadow"
          />
        </div>
        <div class="col-md-6">
          <h2>
            <strong>{{ product.nama }}</strong>
          </h2>
          <hr />
          <h4>
            Harga :
            <strong>Rp. {{ product.harga }}</strong>
          </h4>
          <form class="mt-4" v-on:submit.prevent>
            <div class="form-group">
              <label for="jumlah_pemesanan">Jumlah Pesan</label>
              <input
                type="number"
                class="form-control"
                v-model="pesan.jumlah_pemesanan"
              />
            </div>
            <div class="form-group">
              <label for="keterangan">Keterangan</label>
              <textarea
                v-model="pesan.keterangan"
                class="form-control"
                placeholder="Keterangan spt : Pedes, Nasi Setengah .."
              ></textarea>
            </div>

            <button type="submit" class="btn btn-success" @click="pemesanan">
              <b-icon-cart></b-icon-cart>Pesan
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";

export default {
  name: "FoodDetail",
  components: {
    Navbar,
  },
  data() {
    return {
      product: {},
      pesan: {},
      idproduct: {},
    };
  },
  methods: {
    setProduct(data) {
      this.product = data;
      this.idproduct = data.id;
    },
    pemesanan() {
      if (this.pesan.jumlah_pemesanan) {
        const params = new URLSearchParams();
        params.append("jumlah_pemesanan", this.pesan.jumlah_pemesanan);
        params.append("keterangan", this.pesan.keterangan);
        params.append("products", this.idproduct);

        axios({
          method: "post",
          url: "http://api.wisatanusantara.online/keranjangs.php?function=insert_keranjangs",
          data: params,
        });
        this.$router.push({ path: "/keranjang" });
        this.$toast.success("Sukses Masuk Keranjang", {
          type: "success",
          position: "top-right",
          duration: 3000,
          dismissible: true,
        });
        axios
            .get("https://api.wisatanusantara.online/keranjangs.php?function=get_keranjangs")
            .then((response) => this.setKeranjangs(response.data))
            .catch((error) => console.log(error));
      } else {
        this.$toast.error("Jumlah Pesanan Harus diisi", {
          type: "error",
          position: "top-right",
          duration: 3000,
          dismissible: true,
        });
      }
    },
  },
  mounted() {
    axios
      .get(
        "https://api.wisatanusantara.online/foods.php?function=get_foods_id&id=" +
          this.$route.params.id
      )
      .then((response) => this.setProduct(response.data))
      .catch((error) => console.log(error));
  },
};
</script>

<style>
</style>