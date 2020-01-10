<script>
  export let data;

  const onClick = id => {
    window.location.href = `https://m.amap.com/detail/index/poiid=${id}`;
  };
</script>

<style type="text/scss">
  .hotspot-detail {
    position: fixed;
    bottom: 2%;
    width: 90%;
    left: 5%;
    background: #fff;
    border: 1px solid #e8e8e8;
    min-height: 50px;
    padding: 0.4rem 1rem;
    box-sizing: border-box;

    .name {
      font-size: 16px;
      color: #444;
      margin-bottom: 0.3rem;
    }

    .address {
      font-size: 14px;
      color: #999;
      display: flex;
      justify-content: space-between;

      > span:nth-child(1) {
        max-width: 80%;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }
      > span:nth-child(2) {
        color: #0091ff;
      }
    }
  }
</style>

{#if data.name}
  <div class="hotspot-detail">
    <div class="name">{data.name}</div>
    <div class="address">
      <span>{data.address}</span>
      <span on:click={onClick}>详情 ></span>
    </div>
  </div>
{/if}
