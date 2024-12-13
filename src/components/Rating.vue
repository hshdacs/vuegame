<template>
    <i v-for="i in rate.correct" class="fa-solid fa-star" />
    <i v-for="i in rate.present" class="fa-regular fa-star" />
    <div v-if="(rate.correct + rate.present) == 0">No Hit</div>
</template>

<script>
export default {
    name: "Rating",
    props: {
        rate: {
            number: Number,
            correct: Number,
            present: Number
        }
    }
}
</script>