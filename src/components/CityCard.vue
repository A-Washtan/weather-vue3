<template>
    <div class="flex py-3 px-3 bg-w-secondary rounded-md shadow-md cursor-pointer">
       <div class="flex flex-col flex-1">
        <h2>{{city.city}}</h2>
        <h3>{{city.state}}</h3>
       </div>

       <div class="flex flex-col gap-2">
        <p class="text-3x1 self-end">
            {{Math.round(city.weather.main.temp)}}&deg;
        </p>
        
       </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        type: Object,
        default: () => {},
    }
})
</script>

