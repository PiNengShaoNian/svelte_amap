<script>
  import { onMount } from "svelte";
  import Panel from "./Panel.svelte";
  import HotspotDetail from "./HotspotDetail.svelte";

  let keywords = "";
  let panelShow = false;
  let hotspotDetailShow = false;
  let placeSearch = null;
  let timer = null;
  let map = null;
  let hotspotMarker = null;
  let spotDetail = {};

  const beforeSeach = val => {
    if (!val) return;
    clearTimeout(timer);
    timer = setTimeout(() => {
      placeSearch && placeSearch.search(val);
    }, 500);
  };

  const onFoucs = () => {
    panelShow = true;
  };

  const init = () => {
    map = new window.AMap.Map("container", {
      resizeEnable: true,
      center: [116.397428, 39.90923],
      zoom: 13,
      isHotspot: true
    });

    map.on("hotspotclick", e => {
      const {
        lnglat: { lng, lat },
        id
      } = e;

      const searchSpotDetal = id => {
        placeSearch &&
          placeSearch.getDetails(id, (status, result) => {
            if (status === "complete" && result.info === "OK") {
              spotDetail = result.poiList.pois[0];
            }
          });
      };

      const addMarker = (lng, lat) => {
        const position = new window.AMap.LngLat(lng, lat); // 标准写法
        map.setCenter(position);
        const marker = new window.AMap.Marker({
          position: position
        });

        if (hotspotMarker) {
          map.remove(hotspotMarker);
        }
        hotspotMarker = marker;
        map.add(marker);
      };

      addMarker(lng, lat);
      searchSpotDetal(id);

      hotspotDetailShow = true;
    });

    window.AMap.service(["AMap.PlaceSearch"], () => {
      //构造地点查询类
      placeSearch = new window.AMap.PlaceSearch({
        pageSize: 14, // 单页显示结果条数
        pageIndex: 1, // 页码
        // city: '010', // 兴趣点城市
        // citylimit: true, //是否强制限制在设置的城市内搜索
        map, // 展现结果的地图实例
        panel: "panel", // 结果列表将在此容器中进行展示。
        autoFitView: true // 是否自动调整地图视野使绘制的 Marker点都处于视口的可见范围
      });
    });
  };

  const locateMap = () => {
    map.plugin(["AMap.Geolocation", "AMap.ControlBar"], () => {
      const geolocation = new window.AMap.Geolocation({
        enableHighAccuracy: true,
        timeout: 15000,
        buttonPosition: "RB", // 定位按钮的停靠位置
        buttonOffset: new window.AMap.Pixel(20, 130), // 定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        zoomToAccuracy: true
      });
      map.addControl(geolocation);
      geolocation.getCurrentPosition((status, result) => {});
      window.AMap.event.addListener(geolocation, "error", function(e) {
        console.log(e);
      });
    });
  };

  const closePanel = () => {
    panelShow = false;
  };

  onMount(() => {
    init();
    locateMap();
  });

  $: keywords && beforeSeach(keywords);
</script>

<style type="text/scss">
  #container {
    width: 100%;
    height: calc(100vh - 3.6rem);
    margin-top: 3px;
  }
  .app {
    height: 100%;
    .search-box {
      height: 3.6rem;
      box-sizing: border-box;
      text-align: center;
      display: flex;
      padding: 0.5rem;
      margin-top: 0.5rem;
      > input {
        flex: 1;
        margin-right: 1rem;
        height: 100%;
        border: none;
        padding: 0 1rem;
        outline: none;
        border-bottom: 1px solid #e8e8e8;
      }

      .btns {
        display: flex;
        width: 5rem;
        align-items: center;
        transition: width 0.3s;
        &.lengthen {
          width: 7rem;
        }
      }

      button {
        height: 100%;
        background: #2f86f6;
        border: none;
        border-radius: 3px;
        color: #fff;
        flex: 1;
        outline: none;
        margin-right: 0.8rem;
      }

      .map {
        display: flex;
        flex-direction: column;
        font-size: 12px;
        color: #222;
      }
    }
  }
</style>

<div class="app">
  <div class="search-box">
    <input
      type="text"
      placeholder="请输入地址"
      bind:value={keywords}
      on:focus={onFoucs} />
    <div class={`btns ${panelShow ? 'lengthen' : ''}`}>
      <button>搜索</button>
      {#if panelShow}
        <div class="map" on:click={closePanel}>
          <i class="iconfont icon-map" />
          <span>地图</span>
        </div>
      {/if}
    </div>
  </div>
  <div id="container" />
  <Panel show={panelShow} on:close={closePanel}>
    <div id="panel" />
  </Panel>

  <HotspotDetail data={spotDetail} />

</div>
