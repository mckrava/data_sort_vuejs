<template>
    <transition name="fade">
        <div class="dialog-popup" v-bind:style="{ top: (popup_position_y + 18) + 'px', left: (popup_position_x + 18 ) + 'px' }">
            <div class="sort-section">
                <span class="section-title">Sort</span>
                <button type="button" @click="sortBy(1)">Sort A <ion-icon name="arrow-forward" class="sort-arrow"></ion-icon> Z</button>
                <button type="button" @click="sortBy(-1)">Sort Z <ion-icon name="arrow-back" class="sort-arrow"></ion-icon> A</button>
            </div>
            <div class="filter-section">
                <span class="section-title">Filter</span>
                <div class="filter-text-input-container">
                    <input type="text" placeholder="Search" v-model="filter_options_val" class="filter-text-input">
                    <ion-icon name="search" class="search-icon"></ion-icon>
                </div>
                <div class="filter-options-list-container">
                    <div class="filter-options-list">

                        <div class="option-field">
                            <label for="all">(Select All)
                                <input type="checkbox" name="all" id="all" value="all" :checked="all_selected" @change="selectAllOptions">
                                <span class="checkmark"></span>
                            </label>
                        </div>

                        <div class="option-field" v-for="prop_value in filtered_properties_scope.prop_values" :key="prop_value">
                            <label :for="prop_value">
                                {{ prop_value }}
                                <input type="checkbox" :name="prop_value"
                                       :value="prop_value"
                                       :id="prop_value"
                                       v-model="checked_properties"
                                       :key="prop_value"
                                >
                                <span class="checkmark"></span>
                            </label>
                        </div>

                    </div>
                    <button type="button" @click="clearFilter" class="clear-filter-button">Clear Filter</button>
                </div>
            </div>
            <div class="popup-controls-container">
                <button @click="onFilterAccept">OK</button>
                <button @click="onFilterCancel">Cancel</button>
            </div>
        </div>
    </transition>
</template>

<script>

  import _ from 'lodash'


  export default {
    name: 'SortDialog',
    props: {
      selectedData:     Object,
      propertyKey:      String,
      filters_scope:    Array
    },
    data (){
        return {
          checked_properties:           [],
          order_direction:              false,
          all_selected:                 false,
          filter_options_val:           '',
          filtered_properties_scope:    {},
          popup_position_x:             0,
          popup_position_y:             0
        }
    },
    computed:{

    },
    methods: {
      selectAllOptions() { this.all_selected = !this.all_selected },

      sortBy(order) { this.order_direction = order },

      clearFilter() {
        this.checked_properties = []
        this.all_selected       = false
      },
      onFilterAccept(){
        this.$emit('on_filter_set', {
          prop_name: this.filtered_properties_scope.prop_name,
          prop_values: this.checked_properties
        })

        this.checked_properties = []
      },
      onFilterCancel(){
        this.$emit('on_filter_set', false)
      }
    },
    watch: {
      all_selected (val){
        this.checked_properties = val ? this.filtered_properties_scope.prop_values : []
      },
      filter_options_val(val){
        let filter_key  = val,
          data          = this.selectedData.prop_values

        if (filter_key) {
          data = data.filter(item => String(item).toLowerCase().indexOf(filter_key) > -1 )
        }
        this.filtered_properties_scope.prop_values = data
      },
      order_direction(){
        this.$emit('on_order_set', {
          direction: this.order_direction,
          prop_name: this.selectedData.prop_name
        })
      },
      selectedData(new_data){
        this.filtered_properties_scope  = new_data
        this.popup_position_x           = new_data.el_position.x
        this.popup_position_y           = new_data.el_position.y

        _.forEach(this.filters_scope, item => {
          if (item.prop_name === this.filtered_properties_scope.prop_name) {
            this.checked_properties = [...item.prop_values]
          }
        })
      }
    },
    created(){
      this.filtered_properties_scope    = _.cloneDeep(this.selectedData)
      this.popup_position_x             = this.filtered_properties_scope.el_position.x
      this.popup_position_y             = this.filtered_properties_scope.el_position.y

      _.forEach(this.filters_scope, item => {
        if (item.prop_name === this.filtered_properties_scope.prop_name) {
          this.checked_properties = [...item.prop_values]
        }
      })
    }
  }
</script>


<style lang="scss">

    .custom-popup-control {
        display: block;
        border-radius: 0;
        padding: 5px;
        background-color: rgba(0, 0, 0, 0.1);
        border: 1px solid rgba(0, 0, 0, 0.2);
        box-shadow: none;
        font-weight: 600;
        color: rgba(0, 0, 0, 0.5);
        margin-left: auto;
        cursor: pointer;
        outline: none;

        &:active {
            outline: none;
        }
        &:hover {
            color: #000;
            border: 1px solid rgba(0, 0, 0, 0.5);
        }
    }

    .dialog-popup {
        border: 1px solid #42b983;
        padding: 15px;
        position: fixed;
        top: 0;
        left: 0;
        width: 200px;
        background-color: #fff;
        -webkit-box-shadow: 6px 9px 19px -5px rgba(0,0,0,0.31);
        -moz-box-shadow: 6px 9px 19px -5px rgba(0,0,0,0.31);
        box-shadow: 6px 9px 19px -5px rgba(0,0,0,0.31);

        .section-title {
            font-weight: 600;
            font-size: 14px;
            margin-bottom: 10px;
            display: block;
        }

        .sort-section {
            button {
                display: block;
                border: none;
                box-shadow: none;
                font-size: 14px;
                cursor: pointer;
                margin-bottom: 5px;
                text-align: left;

                &:hover {
                    font-weight: 600;
                }
            }

            .sort-arrow {
                vertical-align: top;
                margin-top: 2px;
            }
        }

        .filter-section {
            margin-top: 20px;

            .clear-filter-button {
                @extend .custom-popup-control;
            }
        }

        .filter-text-input-container {
            position: relative;
            width: 100%;
        }

        .filter-text-input {
            display: block;
            width: 97%;
            height: 23px;
            padding: 2px;
            line-height: 0;
            font-size: 14px;
            outline: none;

            &:active {
                outline: none;
            }
        }

        .search-icon {
            font-size: 22px;
            position: absolute;
            right: 0;
            top: 3px;
        }

        .filter-options-list {
            margin-top: 20px;
            margin-bottom: 10px;
        }

        .option-field {
            label {
                display: block;
                position: relative;
                padding-left: 30px;
                margin-bottom: 12px;
                cursor: pointer;
                font-size: 14px;
                -webkit-user-select: none;
                -moz-user-select: none;
                -ms-user-select: none;
                user-select: none;
            }
            input {
                position: absolute;
                opacity: 0;
                cursor: pointer;
            }
            .checkmark {
                position: absolute;
                top: 0;
                left: 0;
                height: 17px;
                width: 17px;
                background-color: #fff;
                border: 1px solid rgba(0, 0, 0, 0.2);
            }
            .checkmark:after {
                content: "";
                position: absolute;
                display: none;
            }
            &:hover input ~ .checkmark {
                background-color: #ccc;
            }
            input:checked ~ .checkmark:after {
                display: block;
            }
            .checkmark:after {
                left: 6px;
                top: 2px;
                width: 4px;
                height: 9px;
                border: solid #000;
                border-width: 0 2px 2px 0;
                -webkit-transform: rotate(45deg);
                -ms-transform: rotate(45deg);
                transform: rotate(45deg);
            }
        }

        .popup-controls-container {
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid rgba(0, 0, 0, 0.1);

            button {
                @extend .custom-popup-control;
                display: inline-block;
                margin-left: 0;
                margin-right: 10px;
            }
        }
    }




    .fade-enter-active, .fade-leave-active {
        transition: opacity .1s;
    }
    .fade-enter, .fade-leave-to {
        opacity: 0;
    }

</style>
