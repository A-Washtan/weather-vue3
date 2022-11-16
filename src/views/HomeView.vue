<template>
<main  class="container text-white">
    <div
     class="pt-4 mb-8 relative">
        <input 
        @input="getSearchResults"
        v-model="serchQuery"
        type="text" placeholder="أبحث عن المدينة" class="py-2 px-1 w-full bg-transparent border-b focus:border-w-secondary focus:outline-none focus:shadow[0px_1px_0_0_#004E71] " 
        >
        <ul class="absolute bg-w-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
            <p v-if="searcError">يوجد خطأ يرجى المحاولة مرة أخرى</p>
            <p v-if="!searcError && mapboxSearchResults.length === 0">لايوجد نتائج مطابقة يرجى أدخال بيانات صحيحة</p>
         <template v-else>
            <li v-for="searcResult in mapboxSearchResults" :key="searcResult.id" class="py-2 cursor-pointer" 
            @click="previewCity(searcResult)">
            {{searcResult.place_name}}
            </li>
         </template>
        </ul>
    </div>
    <div class="flex flex-col gap-4">
<Suspense>
    <CityList/>
    <template #fallback>
        <p>جاري التحميل ....</p>
    </template>
</Suspense>
    </div>
</main>
</template>


<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";


const router = useRouter()
const previewCity = (searcResult) => {
const [city, state] = searcResult.place_name.split(",");
router.push({
    name:'CityView',
    params: {state: state.replaceAll(" ",""), city: city},
    query: {
        lat: searcResult.geometry.coordinates[1],
        lng: searcResult.geometry.coordinates[0],
        preview: true
    },
});
};

const mapboxAPIKey = "pk.eyJ1IjoiYWJvdWhvciIsImEiOiJjbDhwenE3cWIxNjZ5NDBvNWZ2c3ZtZmZwIn0.eZjWjT_jcD8SwgaXlaQ8eQ"
const serchQuery = ref('')
const qruerTimeout = ref(null)
const mapboxSearchResults = ref(null);
const searcError = ref(null);


const getSearchResults = () => {
    clearTimeout(qruerTimeout.value)
    qruerTimeout.value = setTimeout(async() => {
        if (serchQuery.value !== ""){
            try{
                const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${serchQuery.value}.json?access_token=${mapboxAPIKey}`)
            mapboxSearchResults.value = result.data.features;
            }catch {
                searcError.value = true
            }
           
            return
        }
        mapboxSearchResults.value = null
    },300)
}
</script>