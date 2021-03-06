<script>
  import { VectorTile as VectorTileLayer } from 'ol/layer'
  import RenderType from 'ol/layer/VectorTileRenderType'
  import { isEqual, makeWatchers } from '../../utils'
  import { tileLayer, vectorLayer } from '../../mixins'

  export default {
    name: 'VlLayerVectorTile',
    mixins: [
      tileLayer,
      vectorLayer,
    ],
    props: {
      renderMode: {
        type: String,
        default: RenderType.HYBRID,
        validator: val => Object.values(RenderType).includes(val),
      },
    },
    watch: {
      .../*#__PURE__*/makeWatchers([
        'renderMode',
      ], prop => async function (val, prev) {
        if (isEqual(val, prev)) return

        if (process.env.VUELAYERS_DEBUG) {
          this.$logger.log(`${prop} changed, scheduling recreate...`)
        }

        await this.scheduleRecreate()
      }),
    },
    methods: {
      /**
       * @return {VectorTileLayer}
       * @protected
       */
      createLayer () {
        return new VectorTileLayer({
          // ol/layer/Base
          className: this.className,
          opacity: this.currentOpacity,
          visible: this.currentVisible,
          extent: this.currentExtentViewProj,
          zIndex: this.currentZIndex,
          minResolution: this.currentMinResolution,
          maxResolution: this.currentMaxResolution,
          minZoom: this.currentMinZoom,
          maxZoom: this.currentMaxZoom,
          // ol/layer/Layer
          render: this.render,
          source: this.$source,
          // ol/layer/BaseVector
          renderOrder: this.currentRenderOrder,
          renderBuffer: this.renderBuffer,
          declutter: this.declutter,
          updateWhileAnimating: this.updateWhileAnimating,
          updateWhileInteracting: this.updateWhileInteracting,
          // ol/layer/VectorTile
          // tile layer props
          preload: this.currentPreload,
          useInterimTilesOnError: this.currentUseInterimTilesOnError,
          // vector tile props
          renderMode: this.renderMode,
        })
      },
    },
  }
</script>
