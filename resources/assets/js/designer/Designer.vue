<template>
    <div id="designer-container">
        <toptoolbar ref="toptoolbar"></toptoolbar>
        <div id="designer-subcontainer">
            <toolbar ref="toolbar"></toolbar>
            <div class="canvas-container">
                <designer ref="canvas"></designer>
            </div>
        </div>
    </div>
</template>


<script>
// Import our top-level components

// Designer is our overall canvas tool
import designer from "./components/designer";

// This is our toolbar palette
import toolbar from "./components/toolbar";

// This is our top toolbar with process title and process options
import toptoolbar from "./components/toptoolbar";

export default {
  components: {
    designer,
    toolbar,
    toptoolbar
  }
};
</script>

<style lang="scss" scoped>
#designer-container {
  // Width and height is set to 100% to fill the outside container
  max-height: 100%;
  min-height: 100%;
  max-width: 100%;
  min-width: 100%;
  flex: 1;
  // We will flex container the items
  display: flex;
  flex-direction: column;
  background-color: green;

  #designer-subcontainer {
    display: flex;
    flex: 1;
    flex-direction: row;
    min-width: 100%;
    max-width: 100%;
    min-height: 100%;
    max-height: 100%;


    .canvas-container {
      flex: 1;
      overflow: auto;
    }
  }
}
</style>

