<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-w-secondary w-full text-center">
            <p>
                لقد اخترت المدينة اضغط ( + ) لحفظ المدينة في القائمة الرئيسية
            </p>
        </div>
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{route.params.city}}</h1>
            <p class="text-sm mb-2">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "ar-sa",
                    {
                        weekday:"short",
                        day: "2-digit",
                        month: "long",
                    })
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "ar-sa",
                        {
                          timestyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "en-us",
                    {
                        weekday:"short",
                        day: "2-digit",
                        month: "long",
                    })
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "en-us",
                        {
                          timestyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-8">
               
                {{Math.round(weatherData.current.temp) }}&deg;
            </p>
           
                <p>
                    {{Math.round(weatherData.current.feels_like)}}&deg;
                    <span>تشعر به</span>
                </p>
                <p class="capitalize">
                    {{weatherData.current.weather[0].description}}
                </p>
                <img 
                class="w-[150px] h-auto"
                :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt="">
            
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>

        <!-- الطقس اليومي -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">الطقس اليومي</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.hourly" :key="hourData.dt" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(
                                    hourData.currentTime
                                ).toLocaleTimeString("ar-sa",{
                                    hour:"numeric"
                                })
                            }}
                        </p>
                        <img
                        class="w-auto h-[50px] object-cover"
                        :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" alt="">
                        <p class="text-xl">
                            {{Math.round(hourData.temp)}}&deg;
                        </p>
                        <p class="text-xl"><img src="../assets/humidity.svg" alt="">
                            {{Math.round(hourData.humidity)}}
                        </p>
                        <p class="text-xl"><img src="../assets/wind.svg" alt="">
                            {{Math.round(hourData.wind_speed)}}
                        </p>
                      
                    </div>

                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>
        <!-- الطقس الاسبوعي -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">الطقس الاسبوعي</h2>
                <div
                v-for="day in weatherData.daily" :key="day.dt"
                class="flex items-center"
                >
                <p class="flex-1">
                    {{
                        new Date(day.dt * 1000).toLocaleDateString(
                            "ar-sa",
                            {
                                weekday: "long"
                            }
                        )
                    }}
                </p>
                <img 
                class="w-[50px] h-[50px] object-cover"
                :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt="">
                <div class="flex gap-3 flex-1 justify-end">
                    <p>أعلى: {{Math.round(day.temp.max)}}&deg;</p>
                    <p>أقل: {{Math.round(day.temp.min)}}&deg;</p>
                    <p class="flex gap-2">
                        <img src="../assets/humidity.svg" alt="" class="w-5">
                         {{Math.round(day.humidity)}}</p>
                    <p class="flex gap-2">
                        <img src="../assets/wind.svg" alt="" class="w-5">
                         {{Math.round(day.wind_speed)}}</p>
                </div>
                </div>
            </div>
        </div>

        <!-- حذف المدينة -->
        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
        @click="removeCity">
            <i class="fa-solid fa-trash"></i>
                <p>حذف المدينة</p>
            
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`
        https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.
        lat}&lon=${route.query.lng}&exclude={part}&appid=e96f32126e66ffa9423cf392387ccdd0&units=metric`);


const localOffset = new Date().getTimezoneOffset() * 60000;
const utc = weatherData.data.current.dt * 1000 + localOffset;
weatherData.data.currentTime = 
utc + 1000 * weatherData.data.timezone_offset;

weatherData.data.hourly.forEach((hour) => {
    const utc = hour.dt * 1000 + localOffset;
    hour.currentTime = 
    utc + 1000 * weatherData.data.timezone_offset;
})

return weatherData.data;
    }catch(err) {
        console.log(err)
    }
};

const weatherData =  await getWeatherData();

const router = useRouter()
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"))
    const updateCities = cities.filter(
        (city) => city.id !== route.query.id
    );
    localStorage.setItem(
        "savedCities",
        JSON.stringify(updateCities)
    );
    router.push({
        name: 'home'
    })
}
</script>

