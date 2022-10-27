<template>
  <div style="padding:20px;user-select: none;">
    <div class="switchDate">
      <span class="headBtn bold pointer" @click="setYear(-1)">{{`<`}}</span>
      <span class="headBtn pointer" @click="setMonth(-1)">{{`<`}}</span>
      <span class="font-28" style="margin:0 20px">{{ year }}-{{ month + 1 < 10 ? `0${month + 1}` : month + 1 }}</span>
      <span class="headBtn pointer" @click="setMonth(1)">{{`>`}}</span>
      <span class="headBtn bold pointer" @click="setYear(1)">{{`>`}}</span>
    </div>
    <ul class="day">
      <li v-for="(item,index) in day" :key="index">{{item}}</li>
    </ul>
    <ul class="date">
      <li :style="{gridColumnStart:startWeek}" v-if="startWeek"></li>
      <li 
      v-for="date in dateList" :key="date"
      :class="{active:getActive(date),in:getIn(date),'date-disabled':getDisabled(date)}">
        <span class="dateBlock" @click.stop="clickDate(date)">{{date}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
  const judgeDate = (e,target)=>{
    return (
      new Date(e).getFullYear() === new Date(target).getFullYear() && 
      new Date(e).getMonth() === new Date(target).getMonth() &&
      new Date(e).getDate() === new Date(target).getDate()
    )
  }
  export default{
    props:{
      value:{
        type:Array,
        default:()=>{
          return [new Date(),new Date()]
        }
      },
    },  
    data(){
      return {
        day:["日","一","二","三","四","五","六",],
        year:new Date().getFullYear(),
        inYear:new Date().getFullYear(),
        month:new Date().getMonth(),
        inMonth:new Date().getMonth(),
        date:new Date().getDate(),
        inDay:new Date().getDate(),
        dateGather:this.value
      }
    },
    mounted(){
      const start = new Date(this.dateGather[0])
      const end = new Date(this.dateGather[1]) || new Date(this.dateGather[0])
      let gather = [this.format(start),this.format(end)]
      if(start>end){
        gather = [this.format(end),this.format(start)]
      }
      this.$emit("getDate",gather)
    },  
    computed:{
      startWeek(){
        return new Date(this.year,this.month,1).getDay()
      },  
      dateList(){
        const LASTDATE = new Date(this.year,this.month+1,0).getDate()
        const DATELIST = []
        for(let i=1;i<=LASTDATE;i++){
          DATELIST.push(i)
        }
        return DATELIST
      }
    },
    methods:{
      format(DATE){
        let year = new Date(DATE).getFullYear()
        let month = new Date(DATE).getMonth()+1 < 10 ? `0${new Date(DATE).getMonth()+1}` : new Date(DATE).getMonth()+1
        let date = new Date(DATE).getDate() < 10 ? `0${new Date(DATE).getDate()}` : new Date(DATE).getDate()
        return `${year}-${month}-${date}`
      },
      getActive(date){
        return this.dateGather.some(it=>judgeDate(it,new Date(this.year,this.month,date)))
      },
      getIn(date){
        const time = new Date(this.year,this.month,date).getTime()
        if(this.dateGather.length === 2){
          const MAX = Math.max(new Date(this.dateGather[0]),new Date(this.dateGather[1]))
          const MIN = Math.min(new Date(this.dateGather[0]),new Date(this.dateGather[1]))
          if(MAX > time && MIN < time){
            return true
          }
        }
        return false
      },
      getDisabled(val){
        const inTime = new Date(this.inYear,this.inMonth,this.inDay).getTime(),
              eTime = new Date(this.year,this.month,val).getTime()
        if(inTime<eTime){
          return true
        }
        return false
      },
      clickDate(val){
        if(this.dateGather.length < 2){
          this.dateGather.push(new Date(this.year,this.month,val))
        }else{
          this.dateGather = [new Date(this.year,this.month,val)]
        }
        const start = new Date(this.dateGather[0])
        const end = this.dateGather[1] ? new Date(this.dateGather[1]) : new Date(this.dateGather[0])
        let gather = [this.format(start),this.format(end)]
        if(start>end){
          gather = [this.format(end),this.format(start)]
        }
        this.$emit("getDate",gather)
      },
      setYear(val){
        const D = new Date(this.year+ val,this.month)
        this.year = D.getFullYear()
        this.month = D.getMonth()
      },
      setMonth(val){
        const D = new Date(this.year,this.month + val)
        this.year = D.getFullYear()
        this.month = D.getMonth()
      },
    }
  }
</script>

<style scoped>
*{
  margin:0;
  padding:0;
}
li{
  list-style: none;
}
.day{
  display:grid;
  grid-template-columns: repeat(7,1fr);
  padding:10px 0;
  font-size:12px;
  user-select: none;
}
.date{
  display:grid;
  grid-template-columns: repeat(7,1fr);
  grid-row-gap:16px;
  font-weight: bold;
  font-size:14px;
}
.date li{
  display:flex;
  justify-content: center;
  align-items: center;
}
.dateBlock{
  display:block;
  width:30px;
  height:30px;
  text-align: center;
  line-height: 30px;
}
.active{
  font-weight: bold;
  font-size:16px;
  color:#fff;
}
.dateBlock{
  display:block;
  width:30px;
  height:30px;
  text-align: center;
  line-height: 30px;
  border-radius:50%;
}
.in .dateBlock{
  background:#fdecdd;
}
.active .dateBlock{
  background:#fdb87f;
}
.date-disabled{
  pointer-events: none;
}
.date-disabled .dateBlock{
  background:rgb(231, 231, 231);
  opacity:.5;
}
.bold{
  font-weight: bold;
}
.pointer{
  cursor: pointer;
}
</style>