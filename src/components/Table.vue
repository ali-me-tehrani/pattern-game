<template>
    <div :v-if="!loading" style="background:#1111; padding:18px">
        <div class="row" style="align-items:flex-end;">
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
        <div class="row" v-for="(row, rowIndex) in table" :key="rowIndex">
            <span
                style="height:auto; background:transparent;" 
                v-for="(num, index_num) in horizontalInfo[rowIndex]" 
                :key="index_num" 
                >
                
                    {{num}}
            </span>
            <span 
                v-for="(cell, colIndex) in row" 
                :key="rowIndex+'-'+colIndex" 
                :class="cell%3 == 0  ? 'g' : cell%3 == 1 ? 'b' : 'w'"
                @click="changeState(rowIndex, colIndex)"
                >
            </span>
        </div>
        <div>{{status}}</div>
    </div>
</template>

<script>
import _ from "lodash"
export default {
    name:'Table',
    data () {
        return {
            table : [
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
            ],
            backTable : [
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
                [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ],
            ],
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
    },
    methods:{
        changeState(row, col){
            switch(this.table[row][col]){
                case 0:
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] += 1) 
                    break;
                case -1:
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] += 1) 
                    break;
                case 1:
                    this.$set(this.table, row + "-" + col + "-" + this.count, this.table[row][col] = -1)
                    break; 
            }
            this.backTable[row][col] = this.table[row][col]
            this.count += 1
            this.checkTable()           
        },
        checkTable(){
            if(_.isEqual(this.backTable,this.OriginalTable) )
                this.status = "complet"
        },
        createTable(dimention = 10){
            let table = []
           
            for(let i = 0; i < dimention; i += 1)
                table.push(new Array(dimention).fill(0))

            for(let i = 0; i < dimention; i += 1){
                for(let j = 0; j < dimention; j += 1){
                    if(parseInt(Math.random()*2))
                        table[i][j] = 1
                    else
                        table[i][j] = -1
                }
            }

            this.getInfo(table)

            return table
        },
        getInfo(table){
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

<style>

.flex{
    display: flex;
    flex: 1;
    align-items: center;
    justify-content: center;
    background: #0003;
    padding: 16px;
}

.w{
    background-color: white;
}

.b{
    background-color: black;
}

.row{
    /* width: 400px; */
    display: flex;
    align-items: center;
    justify-content: flex-end;
    margin-right:100px;
}

i{
    display: inline-block;
    width: 15px;
    height: 15px;
    /* padding: 9px; */
    margin: 2px;
}

span{
    display: inline-block;
    background-color: gray;
    width: 15px;
    height: 15px;
    padding: 9px;
    margin: 2px;
}

</style>