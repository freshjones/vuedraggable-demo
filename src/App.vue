<template>
    <div>
        <div v-if="loaded" style="display:flex; width:100%;">
            <div style="flex:1;">
                <nested-draggable
                    :pages="pages"
                ></nested-draggable>
            </div>
            <div style="flex:1;">
                <test-folder-display
                    :value="flat_pages"
                    title="list"
                ></test-folder-display>
            </div>
        </div>
    </div>
</template>

<script>

    import NestedDraggable from "./NestedDraggable";
    import TestFolderDisplay from "./TestFolderDisplay";

    export default {
        components: {
            NestedDraggable,
            TestFolderDisplay
        },
        mounted() {
            this.buildTree();
        },
        methods: {

            buildTree() {
                this.pages = this.map(this.flat_pages).pages;
                this.loaded = true;
            },
            map(data) {
                let map = { pages: [] };
                _.forEach(data, (obj) => {
                    this.merge(map, obj.name, this.split(obj.path))
                });
                return map;
            },

            split(path) {
                let split = _.filter(path.split('/'), (value) => {
                    return ! _.isEmpty(value);
                })
                return split;
            },

            merge(map, name, split) {

                let segment = split[0];

                if (split.length === 1) {
                    if ( ! _.has(map.pages, {id: segment }) ) {
                        map.pages.push({
                            id: segment,
                            name: name,
                            pages: []
                        })
                    }
                } else {

                    let item = _.find(map.pages, {id: segment })

                    if ( typeof item === 'undefined' )
                    {
                        map.pages.push({ id: segment, name: _.capitalize(segment), pages: [] });
                        item = _.last(map.pages);
                    }

                    this.merge(item, name, split.slice(1));

                }

            },

            flattenTree (flat, tree, path) {
                return _.forEach(tree, (obj) => {

                    let flat_path = path + obj.id;

                    flat.push({
                        name: obj.name,
                        path: flat_path,
                    })

                    if(obj.pages.length > 0)
                    {
                        this.flattenTree(flat, obj.pages, flat_path + '/');
                    }

                })
            }

        },
        watch: {
            pages: {
                handler: function (newVal, oldVal) {
                    let flat = [];
                    this.flattenTree(flat, newVal, '/');
                    this.flat_pages = flat;
                },
                deep: true
            }
        },
        data: function() {
            return {
                loaded: false,
                pages: null,
                flat_pages: [
                    {
                        name: "Homepage",
                        path: "/homepage"
                    },
                    {
                        name: "Products",
                        path: "/products"
                    },
                    {
                        name: "Vegetables",
                        path: "/products/vegetables"
                    },
                    {
                        name: "Tomatoes",
                        path: "/products/vegetables/tomatoes"
                    },
                    {
                        name: "Lettuce",
                        path: "/products/vegetables/lettuce"
                    },
                    {
                        name: "Cucumbers",
                        path: "/products/vegetables/cucumbers"
                    },
                    {
                        name: "Fruits",
                        path: "/products/fruits"
                    },
                    {
                        name: "Apples",
                        path: "/products/fruits/apples"
                    },
                    {
                        name: "Red Apples",
                        path: "/products/fruits/apples/red-apples"
                    },
                    {
                        name: "Green Apples",
                        path: "/products/fruits/apples/green-apples"
                    },
                    {
                        name: "Bananas",
                        path: "/products/fruits/bananas"
                    },
                    {
                        name: "Contact Us",
                        path: "/contact-us"
                    },
                    {
                        name: "Red Tomatoes",
                        path: "/products/vegetables/tomatoes/red-tomatoes"
                    },
                ]
            }
        }
    }
</script>
