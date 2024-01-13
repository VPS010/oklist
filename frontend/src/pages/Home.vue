<template>
  <div class=" mx-20 ">


    <div class="flex flex-row items-center justify-between">
      <h2 class="ext-5xl font-black text-gray-900"> Lists</h2>
      <Button iconLeft="plus"> New List</Button>
    </div>


    <div class="mt-2">
      <Card title="General">
        <div>
          <ul>
            <li class="flex flex-row items-center space-y-2 justify-between" v-for="action in actions.data"
              :keys="action.title">
              <span>{{ action.title }}</span>
              <Button @click="completeAction(action.name)" icon="check" />

            </li>
          </ul>

          <Button @click="addActionDialogShown = true" iconLeft="plus-square">New Action</Button>
        </div>
      </Card>
    </div>

    <Dialog :options="{
      title: ' Add New Action',
      actions: [
        {
          label: 'Add Action',
          appearance: 'priamry',
          variant: 'solid',
          handler: ({ close }) => {
            addAction()
            close()
          },
        },
        { label: 'Cancel' },
      ],
    }" v-model="addActionDialogShown">



      <template #body-content>
        <div class="space-y-2">

          <Input type="text" required placeholder="Give title to your Action" label="Title" />
          <Input type="select" :options="categoryOptions" label="Label" />


        </div>
      </template>
    </Dialog>

  </div>
</template>



<script setup>
import { Dialog, createListResource, Card, Input} from 'frappe-ui'
import { reactive, ref ,computed} from 'vue';

const action = reactive({
  title: '',
  category: 'General'
})


const addActionDialogShown = ref(false)



const actions = createListResource({
  doctype: 'OkList',
  fields: ["name", "title", "status", "description", "date", "due_date"],
  filters: {
    status: 'ToDo',
  },
  limit: 100,
})
actions.reload()


const categories = createListResource({
  doctype: 'Category',
  fields: ['name','title'],
  transform(categories){

   return categories.map((c)=>({label: c.title, value: c.name}))

  }
})
categories.reload()


const categoryOptions= computed(()=>{
  if(categories.list.loading || !categories.data){
    return []
  }
   
  return categories.data
})


const completeAction = (actionName) => {
  actions.setValue.submit({
    name: actionName,
    status: 'Completed'
  })
}


const addAction = () => {
  actions.insert.submit(action)

}
</script>

