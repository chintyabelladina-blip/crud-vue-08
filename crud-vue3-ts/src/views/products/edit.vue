<script setup lang="ts">
// Import ref dan onMounted dari vue
import { ref, onMounted } from "vue";

// Import useRoute dan useRouter dari vue-router
import { useRoute, useRouter } from "vue-router";

// Import Api dari folder api
import Api from "../../api";

// Interface Kategori & Errors
interface Kategori {
  id: number;
  name: string;
}

interface Errors {
    name?: string[];
    description?: string[];
    price?: string[];
    stok?: string[];
    id_kategori?: string[];
}

// State untuk form
const name = ref("");
const description = ref("");
const price = ref("");
const stock = ref("");
const kategori_id = ref<number | "">("");

// State list kategori & errors
const categories = ref<Kategori[]>([]);
const errors = ref<Errors>({});

// Initiate route and router
const route = useRoute();
const router = useRouter();

// Fetch daftar kategori untuk dropdown
const fetchCategories = async () => {
  try {
    const response = await Api.get("/api/kategori");
    categories.value = response.data.data || response.data;
  } catch (error) {
    console.error("Error fetching categories:", error);
  }
};

// Fetch detail produk berdasarkan ID
const fetchDetailProduct = async () => {
  try {
    // Memanggil endpoint list atau detail produk
    const response = await Api.get("/api/product");
    const products = response.data.data || response.data;

    // Cari produk berdasarkan ID yang ada di route URL
    const product = Array.isArray(products)
      ? products.find((p: any) => p.id == route.params.id)
      : response.data.data || response.data;

    if (product) {
  name.value = product.name || product.title || "";
  description.value = product.description || "";
  price.value = product.price || "";
  stock.value = product.stok || "";
  kategori_id.value = product.id_kategori || "";
}
  } catch (error) {
    console.error("Error fetching product:", error);
  }
};

// Run hook "onMounted"
onMounted(() => {
  fetchCategories();
  fetchDetailProduct();
});

// Handle form submission (Update Product)
const updateProduct = async () => {
  try {
    await Api.put(`/api/product/${route.params.id}`, {
      name: name.value,
      description: description.value,
      price: Number(price.value),
      stok: Number(stock.value),
      id_kategori: kategori_id.value, // Ubah key dari kategori_id menjadi id_kategori
    });

    router.push("/products");
  } catch (error: any) {
    if (error.response && error.response.data) {
      console.log("Detail Error Validasi:", error.response.data);
      errors.value = error.response.data.errors || error.response.data;
    }
  }
};
</script>

<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-12">
        <div class="card border-0 rounded-3 shadow">
          <div class="card-body">
            <h4 class="fw-bold mb-4">EDIT PRODUCT</h4>
            <form @submit.prevent="updateProduct">
              <!-- Product Name -->
              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input
                  type="text"
                  v-model="name"
                  class="form-control"
                  placeholder="Product Name"
                />
                <div v-if="errors.name" class="alert alert-danger mt-2">
                  {{ errors.name[0] }}
                </div>
              </div>

              <!-- Product Description -->
              <div class="mb-3">
                <label class="form-label fw-bold">Description</label>
                <textarea
                  v-model="description"
                  class="form-control"
                  rows="5"
                  placeholder="Product Description"
                ></textarea>
                <div v-if="errors.description" class="alert alert-danger mt-2">
                  {{ errors.description[0] }}
                </div>
              </div>

              <!-- Category Select -->
              <div class="mb-3">
                <label class="form-label fw-bold">Category</label>
                <select v-model="kategori_id" class="form-select">
                  <option value="" disabled>-- Select Category --</option>
                  <option
                    v-for="kategori in categories"
                    :key="kategori.id"
                    :value="kategori.id"
                  >
                    {{ kategori.name }}
                  </option>
                </select>
               <div v-if="errors.id_kategori" class="alert alert-danger mt-2">
                {{ errors.id_kategori[0] }}
            </div>
              </div>
              
              <div class="row">
                <!-- Price -->
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label fw-bold">Price</label>
                    <input
                      type="number"
                      v-model="price"
                      class="form-control"
                      placeholder="Product Price"
                    />
                    <div v-if="errors.price" class="alert alert-danger mt-2">
                      {{ errors.price[0] }}
                    </div>
                  </div>
                </div>

                <!-- Stock -->
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label fw-bold">Stock</label>
                    <input
                      type="number"
                      v-model="stock"
                      class="form-control"
                      placeholder="Product Stock"
                    />
                    <div v-if="errors.stok" class="alert alert-danger mt-2">
                      {{ errors.stok[0] }}
                    </div>
                  </div>
                </div>
              </div>

              <button
                type="submit"
                class="btn btn-md btn-primary rounded-5 shadow border-0 px-4"
              >
                Update Product
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

