<template>
  <div class="ui container">

    <div class="vuetable-pagination ui basic segment grid">
      <vuetable-pagination-info ref="paginationInfoTop"
      ></vuetable-pagination-info>
      <vuetable-pagination ref="paginationTop"
        @vuetable-pagination:change-page="onChangePage"
      ></vuetable-pagination>
    </div>

    <vuetable ref="vuetable"
      :multi-sort="true"
      :sortOrder="sortOrder"
      api-url="https://vuetable.ratiw.net/api/users"
      :fields="fields"
      pagination-path=""
      :per-page="20"
      @vuetable:pagination-data="onPaginationData"
      detail-row-component="my-detail-row"
      @vuetable:cell-clicked="onCellClicked"
    >
    <template slot="actions" scope="props">
      <div class="custom-actions">
        <button class="ui basic button"
          @click="onAction('view-item', props.rowData, props.rowIndex)">
          <i class="zoom icon"></i>
        </button>
        <button class="ui basic button"
          @click="onAction('edit-item', props.rowData, props.rowIndex)">
          <i class="edit icon"></i>
        </button>
        <button class="ui basic button"
          @click="onAction('delete-item', props.rowData, props.rowIndex)">
          <i class="delete icon"></i>
        </button>
      </div>
    </template>


    </vuetable>

    <div class="vuetable-pagination ui basic segment grid">
      <vuetable-pagination-info ref="paginationInfo">
      </vuetable-pagination-info>
      <vuetable-pagination ref="pagination"
        @vuetable-pagination:change-page="onChangePage"
      ></vuetable-pagination>
    </div>
  </div>
</template>

<script>
import Vuetable from 'vuetable-2/src/components/Vuetable';
import accounting from 'accounting';
import moment from 'moment';
import VuetablePagination from 'vuetable-2/src/components/VuetablePagination';
import VuetablePaginationInfo from 'vuetable-2/src/components/VueTablePaginationInfo';
import Vue from 'vue';
import CustomActions from './CustomActions';
import DetailRow from './DetailRow';

Vue.component('my-detail-row', DetailRow);
Vue.component('custom-actions', CustomActions);
// import VuetablePagination from 'vuetable-2/src/components/VuetablePaginationDropdown'


export default {
  components: {
    Vuetable,
    VuetablePagination,
    VuetablePaginationInfo,
  },
  data() {
    return {
      sortOrder: [
        {
          field: 'email',
          sortField: 'email',
          direction: 'desc',
        }
      ],
      fields: [
        {
          name: '__handle',
          dataClass: 'center aligned'
        },
        {
          name: '__checkbox',
          titleClass: 'center aligned',
          dataClass: 'center aligned',
        },
        {
          name: 'name',
          sortField: 'name'
        },
        {
          name: 'email',
          sortField: 'email'
        },
        {
          name: 'age',
          sortField: 'birthdate',
          // dataClass: 'center aligned',
        },
        {
          name: 'birthdate',
          sortField: 'birthdate',
          titleClass: 'center aligned',
          // dataClass: 'center aligned',
          callback: 'formatDate|DD-MM-YYYY'
        },
        {
          name: 'nickname',
          sortField: 'nickname',
          callback: 'allcap'
        },
        {
          name: 'gender',
          sortField: 'gender',
          titleClass: 'center aligned',
          // dataClass: 'center aligned',
          callback: 'genderLabel'
        },
        {
          name: 'salary',
          sortField: 'salary',
          titleClass: 'center aligned',
          // dataClass: 'right aligned',
          callback: 'formatNumber'
        },
        {
          name: '__component:custom-actions',
          title: 'Actions',
          titleClass: 'center aligned',
          dataClass: 'center aligned'
        },
        // {
        //   name: '__slot:actions',
        //   title: 'Actions',
        //   titleClass: 'center aligned',
        //   dataClass: 'center aligned',
        // },
      ]
    };
  },
  methods: {
    allcap(value) {
      return value.toUpperCase();
    },
    genderLabel(value) {
      return value === 'M'
        ? '<span class="ui teal label"><i class="large man icon"></i>Male</span>'
        : '<span class="ui pink label"><i class="large woman icon"></i>Female</span>';
    },
    formatNumber(value) {
      return accounting.formatNumber(value, 2);
    },
    formatDate(value, fmt = 'D MMM YYYY') {
      return (value == null)
        ? ''
        : moment(value, 'YYYY-MM-DD').format(fmt);
    },
    onPaginationData(paginationData) {
      this.$refs.paginationTop.setPaginationData(paginationData);
      this.$refs.paginationInfoTop.setPaginationData(paginationData);

      this.$refs.pagination.setPaginationData(paginationData);
      this.$refs.paginationInfo.setPaginationData(paginationData);
    },
    onChangePage(page) {
      this.$refs.vuetable.changePage(page);
    },
    onAction(action, data, index) {
      console.log('slot) action: ' + action, data.name, index);
    },
    onCellClicked(data, field, event) {
      console.log('cellClicked: ', field.name);
      this.$refs.vuetable.toggleDetailRow(data.id);
    }
  }
};
</script>