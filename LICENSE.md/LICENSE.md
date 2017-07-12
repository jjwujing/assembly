
;(function($, window, document, undefined) {
    'use strict';
    var pluginName = 'placeholder',
        defaults = {
            className: 'ui-placeholder',
            blurCallback: $.noop,
            focusCallback: $.noop
        };

    /** 
     * 检测是否支持placeholder
     */
    var support = function() {
        var b = 'placeholder' in document.createElement('input');
        return b;
    };

    var PlaceHolder = function(elem, options) {
        this.opts = $.extend({}, defaults, options || {});
        this.$elem = $(elem);
        this.init();
    };

    PlaceHolder.prototype = {
        constructor: PlaceHolder,

        /** 
         * 页面初始化
         */
        init: function() {
            this.render();
        },

        /** 
         * 页面渲染
         */
        render: function() {
        	this.show();
            this.events.focus.call(this);
            this.events.blur.call(this);
        },

        /** 
         * 绑定事件
         */
        events: {

            /** 
             * focus事件
             * time: 2015-02-06 10:32:42
             */
            focus: function() {
                var pass = this.$elem.attr('type') !== 'password';

                if (support() || !pass) return;
                var self = this;
                this.$elem.focus(function() {
                    var $this = $(this);
                    if (this.value === $this.attr('placeholder')) {
                        this.value = '';
                        $this.removeClass(self.opts.className);
	                    if (typeof self.opts.focusCallback === 'function') {
	                    	self.opts.focusCallback.call(this, $(this));
	                    }
                    }
                });
            },

            /** 
             * blur事件
             */
            blur: function() {
                var pass = this.$elem.attr('type') !== 'password';

                if (support() || !pass) return;
                var self = this;
                this.$elem.blur(function() {
                    var $this = $(this);
                    if (this.value === '') {
                        this.value = $this.attr('placeholder') || '';
                    }
                    if (this.value === '' || this.value === $this.attr('placeholder')) {
                        $this.addClass(self.opts.className).val($this.attr('placeholder'));
	                    if (typeof self.opts.blurCallback === 'function') {
	                    	self.opts.blurCallback.call(this, $(this));
	                    }
                    } else if(this.value !== $this.attr('placeholder')) {
                        $this.removeClass(self.opts.className);
                    } else {
                        return false;
                    }
                });
            }
        },

        /** 
         * 显示placeholder
         */
        show: function() {
			alert('');
            var value = this.$elem.val(),
                placeholder = this.$elem.attr('placeholder'),
                pass = this.$elem.attr('type') !== 'password';

            if (!support() && pass && placeholder && value === ''||value===placeholder) {
                this.$elem.addClass(this.opts.className).val(placeholder);
            }
        }
    };

    $.fn[pluginName] = function(options) {
        if (typeof options === 'object' || !options) {
            return this.each(function() {
                var $this = $(this);
                if (!$this.data('plugin_' + pluginName)) {
                    $this.data('plugin_' + pluginName, new PlaceHolder(this, options));
                }
            });
        }
        return this;
    };
}(jQuery, window, document));
