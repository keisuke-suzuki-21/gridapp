<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  data: Array,
  columns: Array,
  filterKey: String
})

const sortKey = ref("")
const sortOrders = ref(
    // ここよくわからん、新しい配列を作ってなんかしてるけど
    props.columns.reduce((o, key) => ((o[key] = 1), o), {})
)

const filteredData = computed(() => {
    let{ data, filterKey } = props

    // searchQueryの処理で、入力された文字と同じ文字列が含まれるgridDataを新しい配列として返す
    if(filterKey){
        filterKey = filterKey.toLocaleLowerCase() //入力をすべて小文字に
        data = data.filter((row) => { //入力値と同じ文字列が含まれるdataを新しい配列に格納
            return Object.keys(row).some((key) => {
                return String(row[key]).toLocaleLowerCase().indexOf(filterKey) > -1
            })
        })
    }

    // gridColumnsをクッリクして昇順、降順を切り替える処理
    const key = sortKey.value
    if(key){
        const order = sortOrders.value[key]
        // 直接dataを切り替えるのではなく、slice()の引数なしでコピーを作ってから入れ替え
        data = data.slice().sort((a,b) => {
            a = a[key]
            b = b[key]
            // aとbを比較
            return (a === b ? 0 : a > b ? 1 : -1) * order
        })
    }

    return data
})

function sortBy(key){
    sortKey.value = key
    sortOrders.value[key] *= -1
}

function capitalize(str){
    return str.charAt(0).toUpperCase() + str.slice(1)
}



</script>

<template>
   <table v-if="filteredData.length">
    <thead>
        <tr>
            <th v-for="key in columns" @click="sortBy(key)">
                {{ capitalize(key) }}
            </th>
        </tr>
    </thead>
    <tbody>
      <tr v-for="entry in filteredData">
        <td v-for="key in columns">
          {{entry[key]}}
        </td>
      </tr>
    </tbody>
   </table>
   <p v-else>No matches found.</p>
</template>


<style>
table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #42b983;
  color: rgba(255, 255, 255, 0.66);
  cursor: pointer;
  user-select: none;
}

td {
  background-color: #f9f9f9;
}

th,
td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}

</style>




