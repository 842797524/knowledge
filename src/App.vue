<template>
  <div>
    <ControlStatus></ControlStatus>
    <NextButtonHook></NextButtonHook>
    <FormPanel></FormPanel>
  </div>
</template>

<script>
  import NextButtonHook from './components/NextButtonHook';
  import ControlStatus from './components/ControlStatus';
  import FormPanel from './components/FormPanel';
  export default {
    components: {
      ControlStatus,
      NextButtonHook,
      FormPanel
    }
  }
</script>

<style lang="scss" scoped>

</style>
