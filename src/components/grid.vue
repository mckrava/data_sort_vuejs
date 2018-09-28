<template>
    <div class="main-grid">
        <FiltersList :filter_data="filter_data" @on_filters_list_change="onFiltersListChange"></FiltersList>

        <Table :data="grid_data" :filters_scope="filters_scope" :order_data="order_data" :is_dialog_open="is_popup_open" @on_sort_dialog_open="onSortDialogOpen"></Table>

        <SortDialog v-if="is_popup_open"
                    :selected-data="selected_property_data"
                    :filters_scope="filters_scope"
                    @on_order_set="onOrderSet"
                    @on_filter_set="onFilterSet"
        >
        </SortDialog>
    </div>
</template>

<script>
  import Table from './table.vue'
  import FiltersList from './filters_list.vue'
  import SortDialog from './dialog_popup.vue'


  export default {
    name: 'Grid',
    components: {
      Table,
      FiltersList,
      SortDialog
    },
    data: () => {
      return {
        grid_data:
          [
            {company_id: 12, company_name: 'Apple', company_city: 'Cupertino', company_country: 'USA', company_category: 'Computer technologies', company_phone: '+1878454'},
            {company_id: 11, company_name: 'Opentech', company_city: 'Cupertino', company_country: 'USA', company_category: 'Computer technologies', company_phone: '+18754545'},
            {company_id: 98, company_name: 'Warephase', company_city: 'New Yourk', company_country: 'USA', company_category: 'Food', company_phone: '+1666666666666'},
            {company_id: 3, company_name: 'Lexiqvolax', company_city: 'Berlin', company_country: 'Germany', company_category: 'Weapon', company_phone: '+456565656'},
          ],
        filter_data:            false,
        filters_scope:          [],
        sort_dialog_open:       false,
        selected_property_data: false,
        order_data:             {},
        is_popup_open:          false
      }
    },
    methods: {
      onSortDialogOpen(property_data){
        this.selected_property_data = property_data
        this.is_popup_open = true
      },
      onFiltersListChange(scope){
        this.filters_scope = scope
      },
      onOrderSet(order){
        this.order_data = order
      },
      onFilterSet(filter_data){

        if (filter_data) {
          this.filter_data = filter_data
          this.is_popup_open = false
        } else {
          this.is_popup_open = false
        }
      }
    }
  }
</script>


<style lang="scss">

    .main-grid {
        width: 100%;
        height: 100%;
        overflow: scroll;
    }

</style>
