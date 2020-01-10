<script>
  export let show = false;
</script>

<style type="text/scss">
  div.show {
    transform: translateY(0);
    transition: all 0.3s;
  }

  .panel {
    position: fixed;
    background: rgba($color: #fff, $alpha: 0.9);
    left: 0;
    top: 4rem;
    width: 100%;
    bottom: 0%;
    z-index: 10000;
    transform: translateY(130%);
    transition: all 0.3s;
    overflow: auto;
    background: #fff;

    .up-btn {
      text-align: center;
      margin: 0;
      margin-bottom: 0.3rem;
      .icon {
        color: #666;
      }
    }
  }
</style>

<div class={`panel ${show ? 'show' : ''}`}>
  <slot />
</div>
