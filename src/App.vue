<template>
    <div id="app">
        <vue-bulma-table :rows="rows" :columns="columns" />

    </div>
</template>

<script>
    import VueBulmaTable from './components/VueBulmaTable'

    export default {
        name: 'App',
        data: function() {
            return {
                rows: [],
                total_rows: 0,
                columns: [{
                        label: "id",
                        name: "id",
                        // filter: {
                        //     type: "simple",
                        //     placeholder: "id"
                        // },
                        sort: true,
                        // row_classes: "myrowclassone myrowclasstwo",
                        // column_classes: "column-class-one column-class-two"
                        // uniqueId: true
                    },
                    {
                        label: "First Name",
                        name: "name.first_name",
                        filter: {
                            type: "multi-select",
                            placeholder: "Enter first name",
                            options: [{
                                    "name": "Bindya",
                                    "value": "Bindya"
                                },
                                {
                                    "name": "option two",
                                    "value": "option two"
                                },
                                {
                                    "name": "option three",
                                    "value": "option three"
                                },
                            ]
                        },
                        sort: true,
                        // slot_name: "my_duplicate_id_column"
                    },
                    {
                        label: "Last Name",
                        name: "name.last_name",
                        filter: {
                            type: "simple",
                            placeholder: "Enter last name",
                            // case_sensitive: true
                        },
                        sort: true,
                    },
                    {
                        label: "Email",
                        name: "email",
                        // filter: {
                        //     type: "simple",
                        //     placeholder: "Enter email"
                        // },
                        sort: true,
                        row_text_alignment: "text-left",
                        column_text_alignment: "text-left",
                        row_classes: "my-row-class1 my-row-class2",
                        column_classes: "my-column-class1 my-column-class2"
                    },
                    {
                        label: "City",
                        name: "address.city",
                        sort: true,
                    },
                    {
                        label: "Country",
                        name: "address.country",
                        // filter: {
                        //     type: "simple",
                        //     placeholder: "Enter country"
                        // },
                    },
                ]
            }
        },
        components: {
            VueBulmaTable
        },
        methods: {
            fetchData() {
                let self = this;
                axios.get('https://raw.githubusercontent.com/rubanraj54/vue-bootstrap4-table/develop/src/assets/toy_data/users_500.json')
                    .then(function(response) {
                        self.rows = response.data;
                    })
                    .catch(function(error) {
                        console.log(error);
                    });
            }
        },
        mounted() {
            this.fetchData();
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
