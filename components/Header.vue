<template>
    <div class="navbar max-h-2 flex items-center">
        <div class="flex-1">
            <a class="btn btn-ghost text-xl flex items-center">
                <img src="https://i.pinimg.com/originals/77/0b/80/770b805d5c99c7931366c2e84e88f251.png" width="40"
                    height="40" class="mr-2">
                Weather App
            </a>
        </div>
        <div class="flex-none">
            <ul class="menu menu-horizontal px-1 flex items-center">
                <li>
                    <DateTime />
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import DateTime from '@/components/CurrentDate&Time.vue';

export default {
    components: { DateTime }
}
</script>