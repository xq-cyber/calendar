<template>
  <div>
    <Calendar v-model="initDate" style="margin-bottom:20px" @getDate="getDate"></Calendar>
  </div>
</template>

<script>
  import Calendar from '@/components/calendar.vue'

  export default {
    components: { Calendar},
    data() {
      return {
        // initDate:[new Date("2022-10-01"),new Date("2022-10-07")]
        initDate:["2022-10-01","2022-10-07"]
      }
    },
    mounted(){
      
    },
    methods:{
      getDate(emitVal){
        console.log(emitVal,"emitVal")
      },
    }
  }
  </script>
  
