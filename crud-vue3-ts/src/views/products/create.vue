  <script setup lang="ts">

// import ref dan onMounted dari vue
import { ref, onMounted } from "vue";

// import useRouter dari vue-router
import { useRouter } from "vue-router";

// import Api dari folder api
import Api from "../../api";

// Interface Kategori
interface Kategori {
    id: number;
    name: string;
}

// Interface Errors
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
const id_kategori = ref<number | "">("");

// State untuk kategori
const kategoris = ref<Kategori[]>([]);

// State untuk error
const errors = ref<Errors>({});

// Router instance
const router = useRouter();

// Ambil data kategori dari API
const fetchKategoris = async () => {
    try {
        const response = await Api.get("/api/kategori");

        console.log("DATA KATEGORI:", response.data);

        kategoris.value = response.data.data ?? response.data;

    } catch (error) {
        console.error("Error fetching kategoris:", error);
    }
};

// Jalankan saat halaman dibuka
onMounted(() => {
    fetchKategoris();
});

// Handle submit form
const storeProduct = async () => {

    // reset error
    errors.value = {};

    // validasi form
    if (!name.value) {
        errors.value.name = ["The name field is required."];
    }

    if (!description.value) {
        errors.value.description = ["The description field is required."];
    }

    if (!price.value) {
        errors.value.price = ["The price field is required."];
    }
if (!stock.value) {
    errors.value.stok = ["The stok field is required."];
}

    if (!id_kategori.value) {
        errors.value.id_kategori = ["The kategori field is required."];
    }

    // jika ada error, hentikan proses
    if (Object.keys(errors.value).length > 0) {
        return;
    }

    // inisialisasi form data
   try {
   const response = await Api.post("/api/product", {
    name: name.value,
    description: description.value,
    price: price.value,
    stok: stock.value,
    id_kategori: id_kategori.value,
});

    console.log("DATA BERHASIL DIKIRIM:", response.data);

    router.push("/products");

} catch (error: any) {
    console.error(
        "ERROR STORE PRODUCT:",
        error.response?.data || error
    );

    errors.value = error.response?.data?.errors || {};
}
};
</script>

<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">

                <div class="card border-0 rounded-3 shadow">
                    <div class="card-body">

                        <form @submit.prevent="storeProduct" novalidate>

                            <!-- Name -->
                            <div class="mb-3">
                                <label class="form-label fw-bold">
                                    Name
                                </label>

                                <input
                                    type="text"
                                    v-model="name"
                                    class="form-control"
                                    placeholder="Name Product"
                                    required
                                />

                                <div
                                    v-if="errors.name"
                                    class="alert alert-danger mt-2"
                                >
                                    {{ errors.name[0] }}
                                </div>
                            </div>


                            <!-- Description -->
                            <div class="mb-3">
                                <label class="form-label fw-bold">
                                    Description
                                </label>

                                <textarea
                                    v-model="description"
                                    class="form-control"
                                    rows="5"
                                    placeholder="Description Product"
                                    required
                                ></textarea>

                                <div
                                    v-if="errors.description"
                                    class="alert alert-danger mt-2"
                                >
                                    {{ errors.description[0] }}
                                </div>
                            </div>


                            <!-- Kategori -->
                            <div class="mb-3">
                                <label class="form-label fw-bold">
                                    Kategori
                                </label>

                                <select
                                    v-model="id_kategori"
                                    class="form-select"
                                    required
                                >
                                    <option value="" disabled>
                                        Select Kategori
                                    </option>

                                    <option
                                        v-for="kategori in kategoris"
                                        :key="kategori.id"
                                        :value="kategori.id"
                                    >
                                        {{ kategori.name }}
                                    </option>
                                </select>

                                <div
                                    v-if="errors.id_kategori"
                                    class="alert alert-danger mt-2"
                                >
                                    {{ errors.id_kategori[0] }}
                                </div>
                            </div>


                            <!-- Price & Stock -->
                            <div class="row">

                                <!-- Price -->
                                <div class="col-md-6">
                                    <div class="mb-3">

                                        <label class="form-label fw-bold">
                                            Price
                                        </label>

                                        <input
                                            type="number"
                                            v-model="price"
                                            class="form-control"
                                            placeholder="Price Product"
                                            required
                                        />

                                        <div
                                            v-if="errors.price"
                                            class="alert alert-danger mt-2"
                                        >
                                            {{ errors.price[0] }}
                                        </div>

                                    </div>
                                </div>


                                <!-- Stock -->
                                <div class="col-md-6">
                                    <div class="mb-3">

                                        <label class="form-label fw-bold">
                                            Stock
                                        </label>

                                        <input
                                            type="number"
                                            v-model="stock"
                                            class="form-control"
                                            placeholder="Stock Product"
                                            required
                                        />

                                       <div
    v-if="errors.stok"
    class="alert alert-danger mt-2"
>
    {{ errors.stok[0] }}
</div>

                                    </div>
                                </div>

                            </div>


                            <!-- Save -->
                            <button
                                type="submit"
                                class="btn btn-md btn-primary rounded-5 shadow border-0"
                            >
                                Save
                            </button>

                        </form>

                    </div>
                </div>

            </div>
        </div>
    </div>
</template>