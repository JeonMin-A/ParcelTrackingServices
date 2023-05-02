<template>
    <button class="opacity-[0.9]">
        <font-awesome-icon :icon="isDark ? 'sun' : 'moon'" class="fixed text-2xl bottom-3 right-5 border border-[#dfdfdf] rounded-lg px-[14.5px] py-2 dark:px-3 bg-[#e6e6e6] dark:bg-[#4b4b4b] dark:border-[#454] darkMode dark:text-white transition-all duration-500 dark:transition-all dark:duration-500 cursor-pointer" @click="$emit('dark')" />
    </button>
    <!-- <font-awesome-icon icon="cloud-sun" class="text-2xl" /> -->
</template>
<script>
export default {
    name: "NavPage",
    data() {
        return {
            
        }
    },
    props:{
    isDark: Boolean
    }
}
</script>
<style>
    
</style>