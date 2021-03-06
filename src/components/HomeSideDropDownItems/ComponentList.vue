<!--
Description:
  Displays list of components in the active route.
  Functionality includes: delete component, set active component, search for component via multiselect.
  -->

<template>
  <div class="home-sidebar">
    <multiselect
      class='multiselect'
      v-model="value"
      :options='options'
      :searchable="true"
      :close-on-select="true"
      :max-height="90"
      :option-height="20"
      @input="handleSelect(value)"
      @open="resetActiveComponent"
      placeholder="Select a component">
      <span slot='noResult'>No components found.</span>
      </multiselect>
      <br/>
    <a
      v-for="componentData in activeRouteDisplay"
      :key="componentData.componentName"
      v-on:click="onActivated(componentData)"
    >
      <q-list class="list-item" dense bordered separator>
        <q-item clickable v-ripple class="list-item">
          <q-item-section>
            <div class="component-container">
              <div class="component-info">
                {{componentData.componentName}}
              </div>
              <q-btn round flat icon="highlight_off" v-on:click.stop="handleClick(componentData)" />
            </div>
          </q-item-section>
        </q-item>
      </q-list>
    </a>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import Multiselect from 'vue-multiselect'

export default {
  data () {
    return {
      value: ''
    }
  },
  components: { Multiselect },
  computed: {
    ...mapState(['routes', 'activeRoute', 'activeComponent']),
    activeRouteDisplay () {
      let component = this.routes[this.activeRoute]
      // console.log('component', component)
      return component
    },
    activeComponentData () {
      return this.activeRouteDisplay.filter(componentData => {
        return componentData.componentName === this.activeComponent
      })[0]
    },
    options () {
      const val = this.activeRouteDisplay.map(component => component.componentName)
      // console.log('options', val)
      return val
    }
  },
  methods: {
    ...mapActions([
      'setActiveComponent',
      'deleteComponent',
      'deleteActiveComponent'
    ]),
    // Set component as active component from left side dropdown
    onActivated (componentData) {
      this.setActiveComponent(componentData.componentName)
      this.activeComponentData.isActive = true
    },
    // Deletes the selected component
    handleClick (componentData) {
      this.setActiveComponent(componentData.componentName)
      this.deleteActiveComponent(componentData.componentName)
    },
    // Select active component from multi-select input
    handleSelect (componentName) {
      this.setActiveComponent(componentName)
      this.value = ''
      this.activeComponentData.isActive = true
    },
    // Deselects active component
    resetActiveComponent () {
      if (this.activeComponent !== '') {
        this.setActiveComponent('')
      }
    }
  }
}
</script>

<style>
/* modifies entire container */
.home-sidebar {
  padding: 0.5rem;
  border-radius: 5px;
}
/* modifies top label */
.component {
  text-transform: uppercase;
}
/* modifies each list element */
.q-list {
  margin-bottom: 0.5rem;
  border-radius: 5px;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.2), 0 3px 6px 0 rgba(0, 0, 0, 0.13);
}
.component-info {
  margin: auto 0;
}
/* modifies content in list element */
.component-container {
  display: flex;
  justify-content: space-between;
}
</style>
