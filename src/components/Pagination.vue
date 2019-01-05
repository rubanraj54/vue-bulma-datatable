<template>
    <div>
        <nav class="pagination is-right" role="navigation" aria-label="pagination">
            <ul class="pagination-list">
                <li :class="{'disabled' : disablePreviousButton}" class="pagination-item" @click.prevent="pageHandler(page-1)">
                    <a class="pagination-link" href="" aria-label="Previous">
                        <span aria-hidden="true">
                                <slot name="vbt-paginataion-previous-button">

                                </slot>
                            </span>
                    </a>
                </li>
                <template v-if="!isEmpty">
                        <li v-if="showLeftDot" @click.prevent="pageHandler(1)">
                            <a class="pagination-link" href=""> 1 </a>
                        </li>
                        <li v-if="showLeftDot">
                            <span class="pagination-ellipsis">&hellip;</span>
                        </li>
                        <li  v-for="index in range" :key="index" @click.prevent="pageHandler(index)">
                            <a class="pagination-link" v-bind:class="{ 'is-current':  (index == page)}" href="">{{index}}</a>
                        </li>
                        <li v-if="showRightDot">
                            <span class="pagination-ellipsis">&hellip;</span>
                        </li>
                        <li v-if="showRightDot" @click.prevent="pageHandler(totalPages)">
                            <a class="pagination-link" href=""> {{totalPages}} </a>
                        </li>
                </template>

                <template v-else>
                    <li>
                        <span class="pagination-ellipsis">&hellip;</span>
                    </li>
                </template>
                <li :class="{'disabled' : disableNextButton}" class="pagination-item" @click.prevent="pageHandler(page+1)">
                    <a class="pagination-link" href="" aria-label="Next">
                        <span aria-hidden="true">
                            <slot name="vbt-paginataion-next-button">

                            </slot>
                        </span>
                    </a>
                </li>
                <!-- Number of rows per page starts here -->
                <div class="dropdown" :class="{'is-active' : dropdown_active}">
                    <div class="dropdown-trigger" @click="dropdown_active = !dropdown_active">
                        <button class="button" aria-haspopup="true" aria-controls="dropdown-menu">
                            <span>{{per_page}}</span>
                            <span class="icon is-small">
                                	&#8964;
                            </span>
                        </button>
                    </div>

                    <div class="dropdown-menu" role="menu">
                        <div s="dropdown-content">
                            <a v-for="(option, key, index) in per_page_options" :key="index" class="dropdown-item" href="" @click.prevent="perPageHandler(option)" v-bind:class="{ 'is-active':  (option == per_page)}">
                                {{option}}
                            </a>
                        </div>
                    </div>
                </div>
                <!-- Number of rows per page ends here -->
                <div class="field vbt-go-to-page">
                    <div class="control">
                        <input class="input is-primary" type="number" placeholder="Go to page" :min="start" :max="totalPages" @keyup.enter="gotoPage" v-model.number="go_to_page">
                    </div>
                </div>
            </ul>
        </nav>
    </div>
</template>

<script>
    import _ from 'lodash';

    export default {
        name: 'Pagination',
        props: {
            page: {
                type: [String, Number],
                required: true
            },
            per_page: {
                type: [String, Number],
                required: true
            },
            total: {
                type: [String, Number],
                required: true
            },
            num_of_visibile_pagination_buttons: {
                type: [String, Number],
                default: 7
            },
            per_page_options: {
                type: Array,
                default: function() {
                    return [5, 10, 15]
                }
            }
        },
        data: function() {
            return {
                start: (this.page + 0),
                end: 0,
                go_to_page: "",
                dropdown_active: false
            }
        },
        mounted() {
            this.end = (this.page + (this.paginationLimit - 1));
        },
        methods: {
            gotoPage() {
                if (this.go_to_page === "") {
                    return;
                }
                let go_to_page = this.go_to_page;
                if (go_to_page >= 1 && go_to_page <= this.totalPages) {
                    this.pageHandler(go_to_page)
                    if (!_.includes(this.range, go_to_page)) {
                        if (this.totalPages - go_to_page < this.num_of_visibile_pagination_buttons) {
                            this.end = this.totalPages;
                            this.start = this.end - (this.num_of_visibile_pagination_buttons - 1);;
                        } else {
                            this.start = go_to_page;
                            this.end = go_to_page + (this.num_of_visibile_pagination_buttons - 1);
                        }
                    }

                } else {
                    console.log("invalid page number");
                }
            },
            pageHandler(index) {
                if (index >= 1 && index <= this.totalPages) {
                    this.$emit('update:page', index);
                }
            },
            perPageHandler(option) {
                this.$emit('update:per_page', option);
                this.dropdown_active = false;
            },
        },
        components: {},
        computed: {
            showLeftDot() {
                return !(_.includes(this.range, 1));
            },
            showRightDot() {
                return !(this.totalPages - this.end <= 0);
            },
            totalPages() {
                return Math.ceil(this.total / this.per_page);
            },
            disablePreviousButton() {
                return this.page == this.start;
            },
            disableNextButton() {
                return this.page == this.end;
            },
            range() {
                return _.range(this.start, (this.end + 1));
            },
            paginationLimit() {
                if (this.totalPages < this.num_of_visibile_pagination_buttons) {
                    return this.totalPages;
                } else {
                    return this.num_of_visibile_pagination_buttons;
                }
            },
            isEmpty() {
                return this.total == 0;
            }

        },
        watch: {
            page(newVal, oldVal) {
                if (newVal == this.totalPages) {
                    this.start = newVal - (this.paginationLimit - 1);
                    this.end = newVal;
                } else if (newVal == 1) {
                    this.start = newVal;
                    this.end = newVal + (this.paginationLimit - 1);
                } else {
                    if (newVal > oldVal) {
                        if (this.end - newVal < 1) {
                            this.start += 1;
                            this.end += 1;
                        }
                    } else {
                        if (this.start - newVal >= 0) {
                            this.start -= 1;
                            this.end -= 1;
                        }
                    }

                }
            },
            rowCount(newVal, oldVal) {
                if (this.page == this.totalPages) {
                    this.start = this.page - (this.paginationLimit - 1);
                    this.end = this.page;
                } else if (this.page == 1) {
                    this.start = this.page;
                    this.end = this.page + (this.paginationLimit - 1);
                }
            },
            totalPages(newVal, oldVal) {
                if (this.page == this.totalPages) {
                    this.start = this.page - (this.paginationLimit - 1);
                    this.end = this.page;
                } else if (this.page == 1) {
                    this.start = this.page;
                    this.end = this.page + (this.paginationLimit - 1);
                }
            },
        }
    }
</script>

<style scoped>
    ul.pagination {
        margin-bottom: 0;
    }

    .vbt-per-page-dropdown {
        margin-left: 8px;
    }

    .vbt-go-to-page {
        margin-left: 8px;
    }
</style>
