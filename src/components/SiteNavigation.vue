<template>
    <header class="sticky top-0 bg-w-primary shadow-lg">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{name: 'home'}">
                <div class="flex items-center gap-3 flex-1">
                <i class="fa-solid fa-sun text-2xl"></i>
                <p class="text-2xl">الطقس </p>
            </div>
            </RouterLink>
           <div class="flex gap-3 flex-1 justify-end">
            <i class="fa-solid fa-circle-info text-xl hover:text-w-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
            <i class="fa-solid fa-plus text-xl hover:text-w-secondary duration-150 cursor-pointer"
            @click="Addcity"
            v-if="route.query.preview"
            ></i>
           </div>
           <BaseModal :modaActive="modaActive" @close-modal="toggleModal">
            <div class="text-black">
                <h1 class="text-2x1 mb-1">عن الخدمة:</h1>
                <p class="mb-4">خدمة الطقس تسمح لك اختيار المدينة ومعرفة تفاصيل الطقس فيه</p>
                <h2 class="text-2x1 mb-1">كيف تعمل:</h2>
                <ol class="list-decimal list-inside mb-4">
                    <li>ابحث عن المدينة بإدخال الاسم في خانة البحث والتأكد من الدولة قبل الاختيار</li>
                    <li>اختر المدينة حتى يتم إظهار البيانات</li>
                    <li>اضغط على "+" حتى يتم حفظ المدينة في الصفحة الرئيسية</li>
                </ol>
                <h2 class="text-2x1">حذف المدينة:</h2>
                <p>إذا أردت أن تحذف المدينة أختر المدينة في الصفحة الرئيسية ثم الدخول عليها حتى يظهر لك خيار الحذف في أسفل الصفحة</p>
            </div>
        </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from 'vue';
import{uid} from 'uid'
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from './BaseModal.vue';

const savedCities = ref ([])
const route = useRoute()
const router = useRouter()
const Addcity = () => {
    if(localStorage.getItem("savedCities")){
        savedCities.value = JSON.parse(localStorage.getItem("savedCities"))
    }
    const locationObj = {
        id: uid(),
        state: route.params.state,
        city:  route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng,
        } 
    }
    savedCities.value.push(locationObj)
    localStorage.setItem(
        "savedCities",
        JSON.stringify(savedCities.value)
    )
    let query = Object.assign({}, route.query)
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query})
}
const modaActive = ref(null)
const toggleModal = () => {
    modaActive.value = !modaActive.value;
}
</script>

