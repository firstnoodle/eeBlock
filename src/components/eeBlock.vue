<template>
    <div class="ee-block" :class="{ 'is-open': isOpen }" :style="computedBlockStyle">
        <div class="ee-block__header" :class="{ 'is-fixed': headerFixed }" ref="header" :style="computedHeaderStyle">
            <slot name="header" />
        </div>
        <div class="ee-block__main" ref="main" :style="computedMainStyle">
            <div class="ee-block__content">
                <slot name="content" />
            </div>
        </div>
        <div v-if="$slots.footer" class="ee-block__footer" :class="{ 'is-fixed': footerFixed }" ref="footer" :style="computedFooterStyle">
            <slot name="footer" />
        </div>
    </div>
</template>

<script>
export default {
    name: 'eeBlock',

    props: {
        closedHeight: {
            type: Number,
            default: 0
        },
        headerFixedOffset: {
            type: Number,
            default: 0,
        },
        initialState: {
            type: String,
            default: 'closed',
            validator: state => {
                return ['open', 'closed'].indexOf(state) !== -1;
            },
        },
    },

    data() {
        return {
            currentScrollY: 0,
            headerFixed: false,
            footerFixed: false,
            isOpen: false,
            mainOffsetHeight: 0,
            footerOffsetLeft: 0,
            mainOffsetWidth: 0,
            ticking: false,
        };
    },

    computed: {
        computedBlockStyle() {
            return this.isOpen && this.footerFixed ? { paddingBottom: this.$refs.footer.offsetHeight + 'px' } : '';
        },

        computedHeaderStyle() {
            if (this.isOpen) {
                return this.headerFixed
                    ? {
                          left: `${this.footerOffsetLeft}px`,
                          position: 'fixed',
                          top: this.headerFixedOffset + 'px',
                          width: `${this.mainOffsetWidth}px`,
                      }
                    : { width: `${this.mainOffsetWidth}px` };
            }
            return '';
        },

        computedMainStyle() {
            return this.isOpen ?  { height: 'auto', paddingTop: this.$refs.header.getBoundingClientRect().height + 'px' } : { height: this.closedHeight + 'px' };
        },

        computedFooterStyle() {
            return this.footerFixed
                ? {
                      bottom: '0px',
                      left: `${this.footerOffsetLeft}px`,
                      position: 'fixed',
                      width: `${this.mainOffsetWidth}px`,
                  }
                : '';
        },
    },

    mounted() {
        if (this.initialState === 'open') this.toggle();
        window.addEventListener('resize', this.handleResize, true);
    },

    methods: {
        handleResize() {
            this.toggle(this.isOpen);
        },
        toggle(state=null) {
            if(state !== null) {
                this.isOpen = state;
            } else {
                this.isOpen = !this.isOpen;
            }

            const mainRect = this.$refs.main.getBoundingClientRect();
            const footerRect = this.$slots.footer ? this.$refs.footer.getBoundingClientRect() : null;

            if (this.isOpen) {
                this.mainOffsetHeight = mainRect.height;
                this.footerOffsetLeft = footerRect ? footerRect.left : null;
                this.mainOffsetWidth = mainRect.width;
                window.addEventListener('scroll', this.handleScroll, true);
                this.handleScroll();
            } else {
                window.removeEventListener('scroll', this.handleScroll, true);
                this.headerFixed = false;
                this.footerFixed = false;
            }
        },

        handleScroll() {
            this.currentScrollY = window.scrollY;
            if (!this.ticking) {
                window.requestAnimationFrame(() => {
                    const headerRect = this.$refs.header.getBoundingClientRect();
                    const mainRect = this.$refs.main.getBoundingClientRect();
                    const footerRect = this.$slots.footer ? this.$refs.footer.getBoundingClientRect() : null;

                    const headerBottom = headerRect.top + headerRect.height + this.currentScrollY;
                    const mainTop = mainRect.top + this.currentScrollY;
                    let mainBottom = mainRect.top + mainRect.height + this.currentScrollY;
                    if(this.$slots.footer) mainBottom += footerRect.height;
                    const viewBottom = this.currentScrollY + window.innerHeight;

                    let bottomLine = mainBottom
                    if(this.$slots.footer) bottomLine -= footerRect.height;
                    // header
                    if (
                        this.isOpen &&
                        mainTop <= this.currentScrollY + this.headerFixedOffset &&
                        this.currentScrollY + headerRect.height + this.headerFixedOffset <
                            bottomLine
                    ) {
                        this.headerFixed = true;
                    } else {
                        this.headerFixed = false;
                    }

                  // footer
                    if(this.$slots.footer) {
                        if (this.isOpen && mainBottom >= viewBottom && headerBottom < viewBottom - footerRect.height) {
                            this.footerFixed = true;
                        } else {
                            this.footerFixed = false;
                        }
                    }
                    this.ticking = false;
                });
            }
            this.ticking = true;
        },
    },
};
</script>

<style lang="scss">
.ee-block {
    box-sizing: border-box;
    display: block;
    margin-bottom: 20px;

    &__header {
        position: relative;
    }

    &__main {
        overflow: hidden;
    }

    &__footer {
        position: relative;
    }
}

.ee-block.is-open {
    & > .ee-block__header {
        position: absolute;

        &.is-fixed {
            box-shadow: 0px 2px 1px 0px rgba(0,0,0,0.2);
        }
    }

    & > .ee-block__main {
        height: auto;
    }

    & > .ee-block__footer {
      &.is-fixed {
        box-shadow: 0px -2px 1px 0px rgba(0,0,0,0.2);
      }
    }
}
</style>
