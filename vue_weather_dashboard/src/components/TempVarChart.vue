<template>
    <div id="tempvarchart">
        <p>Informacje o temperaturze:</p>
        {{tempVar.tempToday}}
    </div>
</template>
<script>
export default {
    props: ["tempVar"],
    components: {},
    data() {
        return {

        }
    },
    methods: {

    },
    computed: {

    },
}
</script>