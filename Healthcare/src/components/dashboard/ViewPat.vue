<template>
  <div class="dashboardContentForms">
  <form id="form-list-client">
    <b-row>
      <b-col md="6" class="my-1">
        <b-form-group horizontal label="Filter" class="mb-0">
          <b-input-group>
            <b-form-input v-model="filter" placeholder="Typ om te zoeken" ></b-form-input>
            <b-input-group-append>
              <b-btn :disabled="!filter" @click="filter = ''" variant="primary">Clear</b-btn>
            </b-input-group-append>
          </b-input-group>
        </b-form-group>
      </b-col>
      <b-col md="6" class="my-1">
        <div class="pull-right" style="padding-top: 10px">
          <b-button size="sm" v-on:click="changeComponent('createPatients')" variant="primary">
            <i class="ion-ios-plus"></i> Patient Aanmaken
          </b-button>
        </div>
      </b-col>
    </b-row>
    <b-table :sort-by.sync="sortBy"
             :sort-desc.sync="sortDesc"
             :items="patients"
             :busy.sync="isBusy"
             :fields="fields"
             :filter="filter"
             style="width: 100%;"
             @filtered="onFiltered"
    >
      <template slot="actions" slot-scope="row">
        <!-- We use @click.stop here to prevent a 'row-clicked' event from also happening -->
        <b-button size="sm" v-if="user.type === 'doctor'" v-on:click="changeComponentUpdate('personalDossier', row.item.user_id)" variant="primary">
          <i style="font-size:24px" class="fa">&#xf06e;</i>
        </b-button>
        <b-button size="sm" v-on:click="changeComponentUpdate('updatePatient', row.item)" variant="primary">
            <i style="font-size:24px" class="fa">&#xf044;</i>
        </b-button>
        <b-button size="sm" variant="primary">
          <i style="font-size:24px" class="fa">&#xf014;</i>
        </b-button>
      </template>
    </b-table>
  </form>
 </div>
</template>

<script>

  export default {
    name: 'patientoverview',
    data() {
      return {
        fields: {
          firstname: {label: 'Voornaam', sortable: true},
          lastname: {label: 'Achternaam', sortable: true},
          age: {label: 'Geboortedatum', sortable: true},
          gender: {label: 'Geslacht', sortable: true},
          actions: {label: 'Acties'}
        },
        user: this.$store.getters.user,
        isBusy: false,
        patients: [],
        totalRows: 0,
        sortBy: null,
        sortDesc: false,
        filter: null
      }
    },
    created () {
      this.isBusy = true;
      this.$store.dispatch("getRequest", 'patients').then((response) => {
        this.isBusy = false;
        this.patients = this.dateConverter(response);
        this.totalRows = this.patients.length;
        this.patients = response
      });
    },
    computed: {
      sortOptions () {
        // Create an options list from our fields
        return this.fields
          .filter(f => f.sortable)
          .map(f => { return { text: f.label, value: f.key } })
      }
    },
    methods: {
      changeComponent (component) {
        this.$parent.changeComponent(component);
      },
      onFiltered (filteredItems) {
        this.totalRows = filteredItems.length
      },
      changeComponentUpdate(component, patient) {
        this.$parent.changeComponent(component, patient);
      },
      dateConverter(values){
        let entries = values;
        for (let index = 0; index < entries.length; ++index) {
          entries[index].age = new Date( parseFloat( entries[index].age)).toLocaleDateString();
        }
        return entries;
      }
    }
  }

</script>

<style>

</style>
