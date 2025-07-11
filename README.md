# CollapsibleMultiSelect

a collabsible multi select component for Vue3 bootstrap5 that follows some principles of UX/XI 
that's not to have a long list of checkboxes where the user will get lost.
A filter field along with select all or none buttons are also there

# Example use

## In the parent component:

// template

```
<div class="col-md-4">
  <CollapsibleMultiSelect v-model="form.categories" :options="categories" label="Categories" class="border p-1 rounded"/>
</div>`
```
//script
```
const categories = ref([])
const form = ref({
    username: null,
    nom: null,
    email: null,
    roles: [],
    sites: [],
    categories: [],
})
```
and of course fetch the categories accordingly.

Some options inside the component are commented, can be used optionally if needed



