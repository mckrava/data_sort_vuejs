<template>
    <div class="filters-list-container">
        <div class="filter-item" v-for="filter in filters_list" :key="filter.prop_name">
            <div class="property-name">{{ filter.prop_name }}</div>
            <div class="property-value" v-for="value in filter.prop_values" :key="value">
                {{ value }}
                <button type="button" class="remove-filter-btn" @click="removeFilter(filter.prop_name, value)"><ion-icon name="close" class="remove-filter-btn-icon"></ion-icon></button>
            </div>
        </div>
        <div class="clear-fix"></div>
    </div>
</template>

<script>

  import _ from 'lodash'


  export default {
    name: 'FiltersList',
    props: {
      filter_data: false
    },
    data(){
      return {
        filters_list: []
      }
    },
    methods: {
      removeFilter(prop_name, value){

        let filters_list        = this.filters_list,
          values_list_length    = 0

        _.forEach(filters_list, item => {
          if (item.prop_name === prop_name) {
            item.prop_values    = _.filter(item.prop_values, val => val !== value)
            values_list_length  = item.prop_values.length
          }
        })

        if (values_list_length === 0) {
          filters_list = _.filter(filters_list, item => item.prop_name !== prop_name)
        }

        this.filters_list   = filters_list

        this.$emit('on-filters-list-change', this.filters_list);
      }
    },
    watch: {
      filter_data(new_val){

        let filters_list    = this.filters_list,
          is_existing_prop  = false

        if (new_val && new_val.prop_values.length > 0) {

            if (filters_list.length > 0) {
              _.forEach(filters_list, item => {
                if (item.prop_name === new_val.prop_name) {
                  item.prop_values = new_val.prop_values
                  is_existing_prop = true
                }
              })

              if (!is_existing_prop){
                filters_list.push(new_val)
              }

            } else {
              filters_list.push(new_val)
            }

        } else if (new_val && new_val.prop_values.length === 0 && filters_list.length > 0) {
          filters_list = _.filter(filters_list, item => item.prop_name !== new_val.prop_name)
        }

        this.filters_list = filters_list

        this.$emit('on_filters_list_change', this.filters_list);

      }
    }
  }
</script>


<style lang="scss">

    .filters-list-container {
        top: 10px;
        border-bottom: 1px solid rgba(0, 0, 0, 0.2);

        .filter-item {
            display: block;
            width: 300px;
            float: left;
            padding: 5px;
            margin: 5px;
            border: 1px solid rgba(0, 0, 0, 0.2);
            background-color: rgba(0, 0, 0, 0.1);
        }

        .property-name {
            margin-bottom: 5px;
            font-weight: 600;
        }

        .property-value {
            display: inline-block;
            padding: 3px 0px 3px 5px;
            border: 1px solid rgba(0, 0, 0, 0.13);
            margin-right: 6px;
            font-size: 12px;
            margin-bottom: 4px;
            background-color: rgba(255, 255, 255, 0.5);
        }

        .remove-filter-btn {
            background-color: transparent;
            border: none;
            box-shadow: none;
            font-size: 12px;
            cursor: pointer;
            line-height: 0;
            vertical-align: top;
            height: 15px;
            outline: none;

            &:active {
                outline: none;
            }

            .remove-filter-btn-icon {
                vertical-align: top;
                margin-top: 1px;
            }

            &:hover {
                .remove-filter-btn-icon {
                    transform: scale(1.3, 1.3);
                }
            }
        }
    }

    .clear-fix {
        clear: both;
    }

</style>
