<template>
    <div class="d-flex flex-column w-100 flex-grow-1 flex-shrink-1">
        <div style="padding: 4px;">
            <div style="float: right;" class="d-flex">
                <input class="mr-2 form-control d-inline-block" @keyup="onQuickFilterChanged" type="text" id="quickFilterInput" placeholder="Type text to filter..."/>
            </div>
            <h1>Users Data</h1>
        </div>
        <div v-if="showGrid" style="width: 100%;">
            <ag-grid-vue style="width: 100%; height: 650px;" class="ag-theme-alpine"
                         :gridOptions="gridOptions"
                         :rowData="rowData"
                         :columnDefs="columnDefs"
                         :modules="modules"
                         :defaultColDef="{
                            sortable: true,
                            resizable: true,
                            filter: true
                         }"

                         @grid-ready="onReady">
            </ag-grid-vue>
        </div>
    </div>
</template>

<script>
    import {AgGridVue} from "@ag-grid-community/vue";
    // for community features
    import {AllCommunityModules} from "@ag-grid-community/all-modules";
    import UserData from './data.json';
    import Axios from 'axios';

    // for enterprise features
    // import {AllModules} from "@ag-grid-enterprise/all-modules";

    export default {
        name: "LargeDataSetExample",
        data() {
            return {
                gridOptions: null,
                api: null,
                showGrid: false,
                rowData: null,
                columnDefs: null,
                modules: AllCommunityModules
            }
        },
        components: {
            'ag-grid-vue': AgGridVue
        },
        methods: {
            onReady(params) {
                console.log('onReady');

                this.api = params.api;

                this.api.sizeColumnsToFit();
            },
            onQuickFilterChanged(event) {
                this.gridOptions.api.setQuickFilter(event.target.value);
            },
        },
        beforeMount() {
            this.gridOptions = {};
            Axios.get('/api/v1/admin/security/users')
            .then(res => {
                let _data = res.data;
                if (_data.data) {
                    this.rowData = _data.data;
                } else 
                    this.rowData = res.data;

                this.columnDefs = Object.freeze([
                    {headerName: '#', field: 'id'},
                    {headerName: 'Avatar', field: 'avatar', cellRenderer: avatarRenderer},
                    {headerName: 'First Name', field: 'first_name'},
                    {headerName: 'Last Name', field: 'last_name'},
                    {headerName: 'Email', field: 'email'},
                    {headerName: 'Status', field: 'status'},
                ])
                this.showGrid = true;
            })
        }
    }

    function avatarRenderer(params) {
        return "<img border='0' width='30' src='" + params.value + "' />"
    }
</script>

<style>
</style>
