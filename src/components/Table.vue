<template>
    <div v-if="!loading" style="background:#1111; padding:18px; height: calc(100vh - 36px)">
        <div class="row">
            <span
                style="height:auto; background:transparent;" 
                v-for="(rowInf, index) in verticalInfo" 
                :key="index" 
                >
                <i 
                    v-for="(num, i) in rowInf" 
                    :key="i+'num'" 
                    >
                    {{num}}
                </i>
            </span>
        </div> 

         <div class="col" >
            <span
                v-for="(rowInf, colIndex) in horizontalInfo" 
                :key="colIndex"
                >
                <i 
                    v-for="(num, i) in rowInf" 
                    :key="i+'num'" 
                    >
                    {{num}}
                </i>
            </span>
        </div> 

        <div class="container" id="container">
            <div
                :class="'g-item ' + (cell%3 == 0  ? 'g' : cell%3 == 1 ? 'b' : 'w')"
                :id="colIndex"
                v-for="(cell, colIndex) in table" 
                :key="colIndex + 'g'" 
                @click="changeState(colIndex)"
            >
            </div>
        </div>

        <div class="modal" v-show="status !== 'not complet'">
            🎉🎊congratulation🎉🎊
        </div>

    </div>
</template>

<script>
import _ from "lodash"

export default {
    name:'Table',
    data () {
        return {
            backTable : new Array(100).fill(0),
            table: new Array(100).fill(0),
            OriginalTable:[],
            verticalInfo:[],
            horizontalInfo:[],
            status:"not complet",
            count:0,
            loading: true
        }
    },
    mounted(){
        this.OriginalTable = this.createTable()
        
        let element = document.getElementById("container")
        let moved
        let startId
        let endId

        let downListener = (event) =>  {
            startId = event.target.id
            moved = false
        }
        element.addEventListener('mousedown', downListener)

        let moveListener = () => {
            moved = true
        }
        element.addEventListener('mousemove', moveListener)
        
        let upListener = (event) => {
            if (moved) {
                endId = event.target.id;
                let start = Math.min(startId, endId)
                let end = Math.max(startId, endId)
                
                if( parseInt(end-start) % 10 === 0 ){
                    for(let i = start; i <= end; i += 10){
                        this.changeState(i)
                    }
                }else if( parseInt(parseInt(end)/10) === parseInt(parseInt(start) / 10) ){
                     for(let i = start; i <= end; i += 1){
                        this.changeState(i)
                    }
                }

            }
        }
        element.addEventListener('mouseup', upListener)
    },
    methods:{
        changeState(index){
            let row = parseInt(index / 10)
            let col = parseInt(index % 10)
            switch(this.table[index]){
                case 0:
                    // this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] += 1) 
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[index] += 1) 
                    break;
                case -1:
                    // this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] += 1) 
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[index] += 1) 
                    break;
                case 1:
                    // this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] = -1)
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[index] -= 2) 
                    break; 
            }
            this.backTable[index] = this.table[index]
            this.count += 1
            this.checkTable()           
        },
        checkTable(){
            if(_.isEqual(this.backTable,this.OriginalTable) )
                this.status = "complet"
        },
        createTable(dimention = 10){
            let table = new Array(dimention*dimention).fill(0)
           
            for(let i = 0; i < table.length; i += 1){
                    if(parseInt(Math.random()*3))
                        table[i] = 1
                    else
                        table[i] = -1
            }

            this.getInfo(table)

            return table
        },
        getInfo(array){
            let table = _.chunk(array, Math.sqrt(array.length))
            let dimention = table.length
            //vertical 
            for(let i = 0; i < dimention; i += 1){
                let num = 0
                let row = []
               
                for(let j = 0; j < dimention; j += 1){
                    if(table[j][i]==1){
                        num += 1
                    }else if( j != 0 && table[j-1][i] == 1 ){
                        row.push(num)
                        num = 0
                    }
                }

                if(num){
                    row.push(num)
                }

                this.verticalInfo.push(row)
            }

            //horizontal
            for(let i = 0; i < dimention; i += 1){
                let num = 0
                let row = []
               
                for(let j = 0; j < dimention; j += 1){
                    if(table[i][j]==1){
                        num += 1
                    }else if( j != 0 && table[i][j-1] == 1 ){
                        row.push(num)
                        num = 0
                    }
                }

                if(num){
                    row.push(num)
                }

                this.horizontalInfo.push(row)
            }
            
            this.loading = false
        }
    }
}
</script>

<style lang="sass">
$borderColor: #454545

.flex
    display: flex 
    flex: 1 
    align-items: flex-start 
    justify-content: flex-start 
    background: #0003 
    padding: 16px 

.w
    background-color: white 

.b
    background-color: black  

.g
    background: #828282

.row
    display: grid 
    grid-template-columns: auto auto auto auto auto auto auto auto auto auto
    align-items: flex-end
    // justify-content: center 
    margin-left: 60px 
    width: 70vw 
    max-width: 400px
    span 
        display: grid

.col
    display: grid 
    grid-template-rows: auto auto auto auto auto auto auto auto auto auto
    margin: auto 
    width: auto
    height: 70vw
    max-height: 400px
    position: absolute
    margin-right: 5px
    span 
        text-align: right
        background: transparent
        align-items: center;
        display: flex;
        justify-content: flex-end;
        
        i
            margin-right: 2px

i
    display: inline-block 

span
    display: inline-block 
    background-color: gray 

.container
    display: grid 
    grid-template-columns: auto auto auto auto auto auto auto auto auto auto 
    padding: 0 10px 
    width: 70vw 
    height: 70vw
    max-height: 400px
    max-width: 400px 
    margin-left: 50px

.modal
    width: 100vw
    height: 100vh
    background: #0007
    position: absolute
    top: 0
    bottom: 0
    right: 0 
    left: 0
    display: flex
    align-items: center
    justify-content: center
    color: #83F52C
    font-size: 4em
    font-weight: bolder


.g-item
  border: 0.5px solid 
  text-align: center 

.g-item:nth-child(5n+0)
    border-right: 2px solid $borderColor 

.g-item:nth-child(10n + 1)
        border-left: 2px solid $borderColor

@for $i from 51 through 60 
    .g-item:nth-child(50n + #{$i})
        border-top: 2px solid $borderColor 

@for $i from 1 through 10 
    .g-item:nth-child(#{$i})
        border-top: 2px solid $borderColor

@for $i from 91 through 100 
    .g-item:nth-child(#{$i})
        border-bottom: 2px solid $borderColor

</style>