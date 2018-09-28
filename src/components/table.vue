<template>
    <div class="table-container">
        <table>
            <thead>
            <tr>
                <th>#</th>
                <th v-for="key in headers_list"
                    :key="key">
                    {{ key }}
                    <span class="open-sort-popup-btn"
                          :class="{ 'active-btn': is_dialog_open && property_key === key }"
                          :ref="key"
                          @click="openSortDialog(key)">
                        <ion-icon name="arrow-dropdown"></ion-icon>
                  </span>
                </th>
                <th v-for="key in (20 - headers_list.length)" :key="key"></th>
            </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in filteredData" :key="item">
                    <td>{{index}}</td>
                    <td v-for="(value, key) in item" :key="key">
                        {{ value }}
                    </td>
                    <td v-for="key in (20 - filteredData.length)" :key="key"></td>
                </tr>
                <tr v-for="key in 30" :key="key">
                    <td v-for="key in 20" :key="key"></td>
                </tr>
            </tbody>
        </table>

    </div>
</template>

<script>

  import _ from 'lodash'

  export default {
    name: 'Table',
    props: {
      data:             Array,
      filters_scope:    Array,
      order_data:       Object,
      is_dialog_open:   Boolean
    },
    data: function() {

      let headers_list = Object.keys(this.data[0]);

      return {
        property_key:           '',
        headers_list:           headers_list,
        sort_direction:         false,
        filter_keys:            []
      }
    },
    computed: {
      filteredData: function () {
        let filters_scope       = this.filters_scope,
          order_prop_name       = this.order_data.prop_name,
          order                 = this.order_data.direction,
          data                  = this.data

        if (filters_scope.length > 0) {
          _.forEach(filters_scope, item => {
            if (item.prop_values.length > 0) {
              data = data.filter(row => _.includes(item.prop_values, row[item.prop_name]))
            }
          })
        }

        if (order) {
          data = data.slice().sort(function (a, b) {
            a = a[order_prop_name]
            b = b[order_prop_name]
            return (a === b ? 0 : a > b ? 1 : -1) * order
          })
        }

        return data
      }
    },
    methods: {
      getSelectedPropertyValues(){
        return _.sortedUniq(this.data.map(item => item[this.property_key]))
      },
      openSortDialog: function (key){

        let current_el_position = this.$refs[key][0].getBoundingClientRect()
        this.property_key       = key

        let data = {
            prop_name:      key,
            prop_values:    this.getSelectedPropertyValues(),
            el_position:    {
              x: current_el_position.x,
              y: current_el_position.y
            }
          }

        this.$emit('on_sort_dialog_open', data)
      }
    }
  }
</script>


<style lang="scss">

    .table-container {
        overflow: scroll;
        width: 100vw;
        height: 100vh;
    }

    table {
        border: 2px solid #42b983;
        border-radius: 3px;
        background-color: #fff;
        border-collapse:collapse;
    }

    table tr td:first-child::after {
        content: "";
        display: inline-block;
        vertical-align: top;
        min-height: 19px;
    }

    th, td {
        min-width: 150px;
        padding: 3px 5px;
        border: 1px solid rgba(0, 0, 0, 0.2);

        &:first-of-type {
            min-width: 20px;
            font-weight: 600;
            text-align: center;
        }
    }


    td {
        background-color: #fff;
    }


    th {
        color: #000;
    }

    th.active {
        color: #fff;
    }

    th.active .arrow {
        opacity: 1;
    }

    .open-sort-popup-btn {
        font-size: 17px;
        line-height: 0;
        vertical-align: middle;
        cursor: pointer;

        &.active-btn {
            background-color: rgba(0, 0, 0, 0.15);
        }

        &:hover {
            background-color: rgba(0, 0, 0, 0.15);
        }

    }

</style>
