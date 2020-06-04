---
layout: default
permalink: /secret/saltapacos/
---
<html>

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1.0, user-scalable=no">
    <title >El Saltapacos</title>
    <style>
    /* Copyright 2013 The Chromium Authors. All rights reserved.
 * Use of this source code is governed by a BSD-style license that can be
 * found in the LICENSE file. */

html, body {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
  background-color: rgb(37, 35, 35);
  color: whitesmoke;
}

.icon {
  -webkit-user-select: none;
  user-select: none;
  display: inline-block;
}

.icon-offline {
  /*content: -webkit-image-set( url(assets/default_100_percent/100-error-offline.png) 1x, url(assets/default_200_percent/200-error-offline.png) 2x);*/
  position: relative;
}

.hidden {
  display: none;
}


/* Offline page */

.offline .interstitial-wrapper {
  color: #2b2b2b;
  font-size: 1em;
  line-height: 1.55;
  margin: 0 auto;
  max-width: 600px;
  padding-top: 100px;
  width: 100%; 
}

.offline .runner-container {
  height: 150px;
  max-width: 600px;
  overflow: hidden;
  position: absolute;
  top: 120px;
  width: 44px;
  border-style: solid;
  border-color: black;
  border-width: 4px;
  background-color: antiquewhite;
}

.offline .runner-canvas {
  height: 150px;
  max-width: 600px;
  opacity: 1;
  overflow: hidden;
  position: absolute;
  top: 0;
  z-index: 2;
}

.offline .controller {
  background: rgba(247, 247, 247, .1);
  height: 100vh;
  left: 0;
  position: absolute;
  top: 0;
  width: 100vw;
  z-index: 1;
}

#offline-resources {
  display: none;
}

@media (max-width: 420px) {
  .suggested-left > #control-buttons, .suggested-right > #control-buttons {
    float: none;
  }
  .snackbar {
    left: 0;
    bottom: 0;
    width: 100%;
    border-radius: 0;
  }
}

@media (max-height: 350px) {
  h1 {
    margin: 0 0 15px;
  }
  .icon-offline {
    margin: 0 0 10px;
  }
  .interstitial-wrapper {
    margin-top: 5%;
  }
  .nav-wrapper {
    margin-top: 30px;
  }
}

@media (min-width: 600px) and (max-width: 736px) and (orientation: landscape) {
  .offline .interstitial-wrapper {
    margin-left: 0;
    margin-right: 0;
  }
}

@media (min-width: 420px) and (max-width: 736px) and (min-height: 240px) and (max-height: 420px) and (orientation:landscape) {
  .interstitial-wrapper {
    margin-bottom: 100px;
  }
}

@media (min-height: 240px) and (orientation: landscape) {
  .offline .interstitial-wrapper {
    margin-bottom: 90px;
  }
  .icon-offline {
    margin-bottom: 20px;
  }
}

@media (max-height: 320px) and (orientation: landscape) {
  .icon-offline {
    margin-bottom: 0;
  }
  .offline .runner-container {
    top: 10px;
  }
}

@media (max-width: 240px) {
  .interstitial-wrapper {
    overflow: inherit;
    padding: 0 8px;
  }
}
.saltapacos-title {
    text-align: center;
    font-family: 'Open Sans', sans-serif;    
}


.saltapacos-text {
    font-family: Arial, Helvetica, sans-serif;
    color: whitesmoke;
}

.saltapacos-link:hover {
    color: cadetblue;
}

.saltapacos-link {
    color: whitesmoke;
    text-decoration: none;
    font-weight: bold;
    border-style: solid;
    border-width: 1px;
    padding-left: 5px;
    padding-right: 5px;
}
</style>
    <link href="https://fonts.googleapis.com/css?family=Open+Sans" rel="stylesheet"> 
    <script>
// Copyright (c) 2014 The Chromium Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.
// extract from chromium source code by @liuwayong
(function () {
    'use strict';
    /**
     * T-Rex runner.
     * @param {string} outerContainerId Outer containing element id.
     * @param {Object} opt_config
     * @constructor
     * @export
     */
    function Runner(outerContainerId, opt_config) {
        // Singleton
        if (Runner.instance_) {
            return Runner.instance_;
        }
        Runner.instance_ = this;

        this.outerContainerEl = document.querySelector(outerContainerId);
        this.containerEl = null;
        this.snackbarEl = null;
        this.detailsButton = this.outerContainerEl.querySelector('#details-button');

        this.config = opt_config || Runner.config;

        this.dimensions = Runner.defaultDimensions;

        this.canvas = null;
        this.canvasCtx = null;

        this.tRex = null;

        this.distanceMeter = null;
        this.distanceRan = 0;

        this.highestScore = 0;

        this.time = 0;
        this.runningTime = 0;
        this.msPerFrame = 1000 / FPS;
        this.currentSpeed = this.config.SPEED;

        this.obstacles = [];

        this.activated = false; // Whether the easter egg has been activated.
        this.playing = false; // Whether the game is currently in play state.
        this.crashed = false;
        this.paused = false;
        this.inverted = false;
        this.invertTimer = 0;
        this.resizeTimerId_ = null;

        this.playCount = 0;

        // Sound FX.
        this.audioBuffer = null;
        this.soundFx = {};

        // Global web audio context for playing sounds.
        this.audioContext = null;

        // Images.
        this.images = {};
        this.imagesLoaded = 0;

        if (this.isDisabled()) {
            this.setupDisabledRunner();
        } else {
            this.loadImages();
        }
    }
    window['Runner'] = Runner;


    /**
     * Default game width.
     * @const
     */
    var DEFAULT_WIDTH = 600;

    /**
     * Frames per second.
     * @const
     */
    var FPS = 60;

    /** @const */
    var IS_HIDPI = window.devicePixelRatio > 1;

    /** @const */
    var IS_IOS = /iPad|iPhone|iPod/.test(window.navigator.platform);

    /** @const */
    var IS_MOBILE = /Android/.test(window.navigator.userAgent) || IS_IOS;

    /** @const */
    var IS_TOUCH_ENABLED = 'ontouchstart' in window;

    /**
     * Default game configuration.
     * @enum {number}
     */
    Runner.config = {
        ACCELERATION: 0.001,
        BG_CLOUD_SPEED: 0.2,
        BOTTOM_PAD: 10,
        CLEAR_TIME: 3000,
        CLOUD_FREQUENCY: 0.5,
        GAMEOVER_CLEAR_TIME: 750,
        GAP_COEFFICIENT: 0.6,
        GRAVITY: 0.6,
        INITIAL_JUMP_VELOCITY: 12,
        INVERT_FADE_DURATION: 12000,
        INVERT_DISTANCE: 700,
        MAX_BLINK_COUNT: 3,
        MAX_CLOUDS: 6,
        MAX_OBSTACLE_LENGTH: 3,
        MAX_OBSTACLE_DUPLICATION: 2,
        MAX_SPEED: 13,
        MIN_JUMP_HEIGHT: 35,
        MOBILE_SPEED_COEFFICIENT: 1.2,
        RESOURCE_TEMPLATE_ID: 'audio-resources',
        SPEED: 6,
        SPEED_DROP_COEFFICIENT: 3
    };


    /**
     * Default dimensions.
     * @enum {string}
     */
    Runner.defaultDimensions = {
        WIDTH: DEFAULT_WIDTH,
        HEIGHT: 150
    };


    /**
     * CSS class names.
     * @enum {string}
     */
    Runner.classes = {
        CANVAS: 'runner-canvas',
        CONTAINER: 'runner-container',
        CRASHED: 'crashed',
        ICON: 'icon-offline',
        INVERTED: 'inverted',
        SNACKBAR: 'snackbar',
        SNACKBAR_SHOW: 'snackbar-show',
        TOUCH_CONTROLLER: 'controller'
    };


    /**
     * Sprite definition layout of the spritesheet.
     * @enum {Object}
     */
    Runner.spriteDefinition = {
        LDPI: {
            CACTUS_LARGE: { x: 332, y: 2 },
            CACTUS_SMALL: { x: 228, y: 2 },
            CLOUD: { x: 86, y: 2 },
            HORIZON: { x: 2, y: 54 },
            MOON: { x: 484, y: 2 },
            PTERODACTYL: { x: 134, y: 2 },
            RESTART: { x: 2, y: 2 },
            TEXT_SPRITE: { x: 655, y: 2 },
            TREX: { x: 846, y: 0 },
            STAR: { x: 645, y: 2 }
        },
        HDPI: {
            CACTUS_LARGE: { x: 652, y: 2 },
            CACTUS_SMALL: { x: 446, y: 2 },
            CLOUD: { x: 166, y: 2 },
            HORIZON: { x: 2, y: 104 },
            MOON: { x: 954, y: 2 },
            PTERODACTYL: { x: 260, y: 2 },
            RESTART: { x: 2, y: 2 },
            TEXT_SPRITE: { x: 1294, y: 2 },
            TREX: { x: 1678, y: 0 },
            STAR: { x: 1276, y: 2 }
        }
    };


    /**
     * Sound FX. Reference to the ID of the audio tag on interstitial page.
     * @enum {string}
     */
    Runner.sounds = {
        BUTTON_PRESS: 'offline-sound-press',
        HIT: 'offline-sound-hit',
        SCORE: 'offline-sound-reached'
    };


    /**
     * Key code mapping.
     * @enum {Object}
     */
    Runner.keycodes = {
        JUMP: { '38': 1, '32': 1 },  // Up, spacebar
        DUCK: { '40': 1 },  // Down
        RESTART: { '13': 1 }  // Enter
    };


    /**
     * Runner event names.
     * @enum {string}
     */
    Runner.events = {
        ANIM_END: 'webkitAnimationEnd',
        CLICK: 'click',
        KEYDOWN: 'keydown',
        KEYUP: 'keyup',
        MOUSEDOWN: 'mousedown',
        MOUSEUP: 'mouseup',
        RESIZE: 'resize',
        TOUCHEND: 'touchend',
        TOUCHSTART: 'touchstart',
        VISIBILITY: 'visibilitychange',
        BLUR: 'blur',
        FOCUS: 'focus',
        LOAD: 'load'
    };


    Runner.prototype = {
        /**
         * Whether the easter egg has been disabled. CrOS enterprise enrolled devices.
         * @return {boolean}
         */
        isDisabled: function () {
            // return loadTimeData && loadTimeData.valueExists('disabledEasterEgg');
            return false;
        },

        /**
         * For disabled instances, set up a snackbar with the disabled message.
         */
        setupDisabledRunner: function () {
            this.containerEl = document.createElement('div');
            this.containerEl.className = Runner.classes.SNACKBAR;
            this.containerEl.textContent = loadTimeData.getValue('disabledEasterEgg');
            this.outerContainerEl.appendChild(this.containerEl);

            // Show notification when the activation key is pressed.
            document.addEventListener(Runner.events.KEYDOWN, function (e) {
                if (Runner.keycodes.JUMP[e.keyCode]) {
                    this.containerEl.classList.add(Runner.classes.SNACKBAR_SHOW);
                    document.querySelector('.icon').classList.add('icon-disabled');
                }
            }.bind(this));
        },

        /**
         * Setting individual settings for debugging.
         * @param {string} setting
         * @param {*} value
         */
        updateConfigSetting: function (setting, value) {
            if (setting in this.config && value != undefined) {
                this.config[setting] = value;

                switch (setting) {
                    case 'GRAVITY':
                    case 'MIN_JUMP_HEIGHT':
                    case 'SPEED_DROP_COEFFICIENT':
                        this.tRex.config[setting] = value;
                        break;
                    case 'INITIAL_JUMP_VELOCITY':
                        this.tRex.setJumpVelocity(value);
                        break;
                    case 'SPEED':
                        this.setSpeed(value);
                        break;
                }
            }
        },

        /**
         * Cache the appropriate image sprite from the page and get the sprite sheet
         * definition.
         */
        loadImages: function () {
            if (IS_HIDPI) {
                Runner.imageSprite = document.getElementById('offline-resources-2x');
                this.spriteDef = Runner.spriteDefinition.HDPI;
            } else {
                Runner.imageSprite = document.getElementById('offline-resources-1x');
                this.spriteDef = Runner.spriteDefinition.LDPI;
            }

            if (Runner.imageSprite.complete) {
                this.init();
            } else {
                // If the images are not yet loaded, add a listener.
                Runner.imageSprite.addEventListener(Runner.events.LOAD,
                    this.init.bind(this));
            }
        },

        /**
         * Load and decode base 64 encoded sounds.
         */
        loadSounds: function () {
            if (!IS_IOS) {
                this.audioContext = new AudioContext();

                var resourceTemplate =
                    document.getElementById(this.config.RESOURCE_TEMPLATE_ID).content;

                for (var sound in Runner.sounds) {
                    var soundSrc =
                        resourceTemplate.getElementById(Runner.sounds[sound]).src;
                    soundSrc = soundSrc.substr(soundSrc.indexOf(',') + 1);
                    var buffer = decodeBase64ToArrayBuffer(soundSrc);

                    // Async, so no guarantee of order in array.
                    this.audioContext.decodeAudioData(buffer, function (index, audioData) {
                        this.soundFx[index] = audioData;
                    }.bind(this, sound));
                }
            }
        },

        /**
         * Sets the game speed. Adjust the speed accordingly if on a smaller screen.
         * @param {number} opt_speed
         */
        setSpeed: function (opt_speed) {
            var speed = opt_speed || this.currentSpeed;

            // Reduce the speed on smaller mobile screens.
            if (this.dimensions.WIDTH < DEFAULT_WIDTH) {
                var mobileSpeed = speed * this.dimensions.WIDTH / DEFAULT_WIDTH *
                    this.config.MOBILE_SPEED_COEFFICIENT;
                this.currentSpeed = mobileSpeed > speed ? speed : mobileSpeed;
            } else if (opt_speed) {
                this.currentSpeed = opt_speed;
            }
        },

        /**
         * Game initialiser.
         */
        init: function () {
            // Hide the static icon.
            document.querySelector('.' + Runner.classes.ICON).style.visibility =
                'hidden';

            this.adjustDimensions();
            this.setSpeed();

            this.containerEl = document.createElement('div');
            this.containerEl.className = Runner.classes.CONTAINER;

            // Player canvas container.
            this.canvas = createCanvas(this.containerEl, this.dimensions.WIDTH,
                this.dimensions.HEIGHT, Runner.classes.PLAYER);

            this.canvasCtx = this.canvas.getContext('2d');
            this.canvasCtx.fillStyle = '#f7f7f7';
            this.canvasCtx.fill();
            Runner.updateCanvasScaling(this.canvas);

            // Horizon contains clouds, obstacles and the ground.
            this.horizon = new Horizon(this.canvas, this.spriteDef, this.dimensions,
                this.config.GAP_COEFFICIENT);

            // Distance meter
            this.distanceMeter = new DistanceMeter(this.canvas,
                this.spriteDef.TEXT_SPRITE, this.dimensions.WIDTH);

            // Draw t-rex
            this.tRex = new Trex(this.canvas, this.spriteDef.TREX);

            this.outerContainerEl.appendChild(this.containerEl);

            if (IS_MOBILE) {
                this.createTouchController();
            }

            this.startListening();
            this.update();

            window.addEventListener(Runner.events.RESIZE,
                this.debounceResize.bind(this));
        },

        /**
         * Create the touch controller. A div that covers whole screen.
         */
        createTouchController: function () {
            this.touchController = document.createElement('div');
            this.touchController.className = Runner.classes.TOUCH_CONTROLLER;
            this.outerContainerEl.appendChild(this.touchController);
        },

        /**
         * Debounce the resize event.
         */
        debounceResize: function () {
            if (!this.resizeTimerId_) {
                this.resizeTimerId_ =
                    setInterval(this.adjustDimensions.bind(this), 250);
            }
        },

        /**
         * Adjust game space dimensions on resize.
         */
        adjustDimensions: function () {
            clearInterval(this.resizeTimerId_);
            this.resizeTimerId_ = null;

            var boxStyles = window.getComputedStyle(this.outerContainerEl);
            var padding = Number(boxStyles.paddingLeft.substr(0,
                boxStyles.paddingLeft.length - 2));

            this.dimensions.WIDTH = this.outerContainerEl.offsetWidth - padding * 2;

            // Redraw the elements back onto the canvas.
            if (this.canvas) {
                this.canvas.width = this.dimensions.WIDTH;
                this.canvas.height = this.dimensions.HEIGHT;

                Runner.updateCanvasScaling(this.canvas);

                this.distanceMeter.calcXPos(this.dimensions.WIDTH);
                this.clearCanvas();
                this.horizon.update(0, 0, true);
                this.tRex.update(0);

                // Outer container and distance meter.
                if (this.playing || this.crashed || this.paused) {
                    this.containerEl.style.width = this.dimensions.WIDTH + 'px';
                    this.containerEl.style.height = this.dimensions.HEIGHT + 'px';
                    this.distanceMeter.update(0, Math.ceil(this.distanceRan));
                    this.stop();
                } else {
                    this.tRex.draw(0, 0);
                }

                // Game over panel.
                if (this.crashed && this.gameOverPanel) {
                    this.gameOverPanel.updateDimensions(this.dimensions.WIDTH);
                    this.gameOverPanel.draw();
                }
            }
        },

        /**
         * Play the game intro.
         * Canvas container width expands out to the full width.
         */
        playIntro: function () {
            if (!this.activated && !this.crashed) {
                this.playingIntro = true;
                this.tRex.playingIntro = true;

                // CSS animation definition.
                var keyframes = '@-webkit-keyframes intro { ' +
                    'from { width:' + Trex.config.WIDTH + 'px }' +
                    'to { width: ' + this.dimensions.WIDTH + 'px }' +
                    '}';
                
                // create a style sheet to put the keyframe rule in 
                // and then place the style sheet in the html head    
                var sheet = document.createElement('style');
                sheet.innerHTML = keyframes;
                document.head.appendChild(sheet);

                this.containerEl.addEventListener(Runner.events.ANIM_END,
                    this.startGame.bind(this));

                this.containerEl.style.webkitAnimation = 'intro .4s ease-out 1 both';
                this.containerEl.style.width = this.dimensions.WIDTH + 'px';

                // if (this.touchController) {
                //     this.outerContainerEl.appendChild(this.touchController);
                // }
                this.playing = true;
                this.activated = true;
            } else if (this.crashed) {
                this.restart();
            }
        },


        /**
         * Update the game status to started.
         */
        startGame: function () {
            this.runningTime = 0;
            this.playingIntro = false;
            this.tRex.playingIntro = false;
            this.containerEl.style.webkitAnimation = '';
            this.playCount++;

            // Handle tabbing off the page. Pause the current game.
            document.addEventListener(Runner.events.VISIBILITY,
                this.onVisibilityChange.bind(this));

            window.addEventListener(Runner.events.BLUR,
                this.onVisibilityChange.bind(this));

            window.addEventListener(Runner.events.FOCUS,
                this.onVisibilityChange.bind(this));
        },

        clearCanvas: function () {
            this.canvasCtx.clearRect(0, 0, this.dimensions.WIDTH,
                this.dimensions.HEIGHT);
        },

        /**
         * Update the game frame and schedules the next one.
         */
        update: function () {
            this.updatePending = false;

            var now = getTimeStamp();
            var deltaTime = now - (this.time || now);
            this.time = now;

            if (this.playing) {
                this.clearCanvas();

                if (this.tRex.jumping) {
                    this.tRex.updateJump(deltaTime);
                }

                this.runningTime += deltaTime;
                var hasObstacles = this.runningTime > this.config.CLEAR_TIME;

                // First jump triggers the intro.
                if (this.tRex.jumpCount == 1 && !this.playingIntro) {
                    this.playIntro();
                }

                // The horizon doesn't move until the intro is over.
                if (this.playingIntro) {
                    this.horizon.update(0, this.currentSpeed, hasObstacles);
                } else {
                    deltaTime = !this.activated ? 0 : deltaTime;
                    this.horizon.update(deltaTime, this.currentSpeed, hasObstacles,
                        this.inverted);
                }

                // Check for collisions.
                var collision = hasObstacles &&
                    checkForCollision(this.horizon.obstacles[0], this.tRex);

                if (!collision) {
                    this.distanceRan += this.currentSpeed * deltaTime / this.msPerFrame;

                    if (this.currentSpeed < this.config.MAX_SPEED) {
                        this.currentSpeed += this.config.ACCELERATION;
                    }
                } else {
                    this.gameOver();
                }

                var playAchievementSound = this.distanceMeter.update(deltaTime,
                    Math.ceil(this.distanceRan));

                if (playAchievementSound) {
                    this.playSound(this.soundFx.SCORE);
                }

                // Night mode.
                if (this.invertTimer > this.config.INVERT_FADE_DURATION) {
                    this.invertTimer = 0;
                    this.invertTrigger = false;
                    this.invert();
                } else if (this.invertTimer) {
                    this.invertTimer += deltaTime;
                } else {
                    var actualDistance =
                        this.distanceMeter.getActualDistance(Math.ceil(this.distanceRan));

                    if (actualDistance > 0) {
                        this.invertTrigger = !(actualDistance %
                            this.config.INVERT_DISTANCE);

                        if (this.invertTrigger && this.invertTimer === 0) {
                            this.invertTimer += deltaTime;
                            this.invert();
                        }
                    }
                }
            }

            if (this.playing || (!this.activated &&
                this.tRex.blinkCount < Runner.config.MAX_BLINK_COUNT)) {
                this.tRex.update(deltaTime);
                this.scheduleNextUpdate();
            }
        },

        /**
         * Event handler.
         */
        handleEvent: function (e) {
            return (function (evtType, events) {
                switch (evtType) {
                    case events.KEYDOWN:
                    case events.TOUCHSTART:
                    case events.MOUSEDOWN:
                        this.onKeyDown(e);
                        break;
                    case events.KEYUP:
                    case events.TOUCHEND:
                    case events.MOUSEUP:
                        this.onKeyUp(e);
                        break;
                }
            }.bind(this))(e.type, Runner.events);
        },

        /**
         * Bind relevant key / mouse / touch listeners.
         */
        startListening: function () {
            // Keys.
            document.addEventListener(Runner.events.KEYDOWN, this);
            document.addEventListener(Runner.events.KEYUP, this);

            if (IS_MOBILE) {
                // Mobile only touch devices.
                this.touchController.addEventListener(Runner.events.TOUCHSTART, this);
                this.touchController.addEventListener(Runner.events.TOUCHEND, this);
                this.containerEl.addEventListener(Runner.events.TOUCHSTART, this);
            } else {
                // Mouse.
                document.addEventListener(Runner.events.MOUSEDOWN, this);
                document.addEventListener(Runner.events.MOUSEUP, this);
            }
        },

        /**
         * Remove all listeners.
         */
        stopListening: function () {
            document.removeEventListener(Runner.events.KEYDOWN, this);
            document.removeEventListener(Runner.events.KEYUP, this);

            if (IS_MOBILE) {
                this.touchController.removeEventListener(Runner.events.TOUCHSTART, this);
                this.touchController.removeEventListener(Runner.events.TOUCHEND, this);
                this.containerEl.removeEventListener(Runner.events.TOUCHSTART, this);
            } else {
                document.removeEventListener(Runner.events.MOUSEDOWN, this);
                document.removeEventListener(Runner.events.MOUSEUP, this);
            }
        },

        /**
         * Process keydown.
         * @param {Event} e
         */
        onKeyDown: function (e) {
            // Prevent native page scrolling whilst tapping on mobile.
            if (IS_MOBILE && this.playing) {
                e.preventDefault();
            }

            if (e.target != this.detailsButton) {
                if (!this.crashed && (Runner.keycodes.JUMP[e.keyCode] ||
                    e.type == Runner.events.TOUCHSTART)) {
                    if (!this.playing) {
                        this.loadSounds();
                        this.playing = true;
                        this.update();
                        if (window.errorPageController) {
                            errorPageController.trackEasterEgg();
                        }
                    }
                    //  Play sound effect and jump on starting the game for the first time.
                    if (!this.tRex.jumping && !this.tRex.ducking) {
                        this.playSound(this.soundFx.BUTTON_PRESS);
                        this.tRex.startJump(this.currentSpeed);
                    }
                }

                if (this.crashed && e.type == Runner.events.TOUCHSTART &&
                    e.currentTarget == this.containerEl) {
                    this.restart();
                }
            }

            if (this.playing && !this.crashed && Runner.keycodes.DUCK[e.keyCode]) {
                e.preventDefault();
                if (this.tRex.jumping) {
                    // Speed drop, activated only when jump key is not pressed.
                    this.tRex.setSpeedDrop();
                } else if (!this.tRex.jumping && !this.tRex.ducking) {
                    // Duck.
                    this.tRex.setDuck(true);
                }
            }
        },


        /**
         * Process key up.
         * @param {Event} e
         */
        onKeyUp: function (e) {
            var keyCode = String(e.keyCode);
            var isjumpKey = Runner.keycodes.JUMP[keyCode] ||
                e.type == Runner.events.TOUCHEND ||
                e.type == Runner.events.MOUSEDOWN;

            if (this.isRunning() && isjumpKey) {
                this.tRex.endJump();
            } else if (Runner.keycodes.DUCK[keyCode]) {
                this.tRex.speedDrop = false;
                this.tRex.setDuck(false);
            } else if (this.crashed) {
                // Check that enough time has elapsed before allowing jump key to restart.
                var deltaTime = getTimeStamp() - this.time;

                if (Runner.keycodes.RESTART[keyCode] || this.isLeftClickOnCanvas(e) ||
                    (deltaTime >= this.config.GAMEOVER_CLEAR_TIME &&
                        Runner.keycodes.JUMP[keyCode])) {
                    this.restart();
                }
            } else if (this.paused && isjumpKey) {
                // Reset the jump state
                this.tRex.reset();
                this.play();
            }
        },

        /**
         * Returns whether the event was a left click on canvas.
         * On Windows right click is registered as a click.
         * @param {Event} e
         * @return {boolean}
         */
        isLeftClickOnCanvas: function (e) {
            return e.button != null && e.button < 2 &&
                e.type == Runner.events.MOUSEUP && e.target == this.canvas;
        },

        /**
         * RequestAnimationFrame wrapper.
         */
        scheduleNextUpdate: function () {
            if (!this.updatePending) {
                this.updatePending = true;
                this.raqId = requestAnimationFrame(this.update.bind(this));
            }
        },

        /**
         * Whether the game is running.
         * @return {boolean}
         */
        isRunning: function () {
            return !!this.raqId;
        },

        /**
         * Game over state.
         */
        gameOver: function () {
            this.playSound(this.soundFx.HIT);
            vibrate(200);

            this.stop();
            this.crashed = true;
            this.distanceMeter.acheivement = false;

            this.tRex.update(100, Trex.status.CRASHED);

            // Game over panel.
            if (!this.gameOverPanel) {
                this.gameOverPanel = new GameOverPanel(this.canvas,
                    this.spriteDef.TEXT_SPRITE, this.spriteDef.RESTART,
                    this.dimensions);
            } else {
                this.gameOverPanel.draw();
            }

            // Update the high score.
            if (this.distanceRan > this.highestScore) {
                this.highestScore = Math.ceil(this.distanceRan);
                this.distanceMeter.setHighScore(this.highestScore);
            }

            // Reset the time clock.
            this.time = getTimeStamp();
        },

        stop: function () {
            this.playing = false;
            this.paused = true;
            cancelAnimationFrame(this.raqId);
            this.raqId = 0;
        },

        play: function () {
            if (!this.crashed) {
                this.playing = true;
                this.paused = false;
                this.tRex.update(0, Trex.status.RUNNING);
                this.time = getTimeStamp();
                this.update();
            }
        },

        restart: function () {
            if (!this.raqId) {
                this.playCount++;
                this.runningTime = 0;
                this.playing = true;
                this.crashed = false;
                this.distanceRan = 0;
                this.setSpeed(this.config.SPEED);
                this.time = getTimeStamp();
                this.containerEl.classList.remove(Runner.classes.CRASHED);
                this.clearCanvas();
                this.distanceMeter.reset(this.highestScore);
                this.horizon.reset();
                this.tRex.reset();
                this.playSound(this.soundFx.BUTTON_PRESS);
                this.invert(true);
                this.update();
            }
        },

        /**
         * Pause the game if the tab is not in focus.
         */
        onVisibilityChange: function (e) {
            if (document.hidden || document.webkitHidden || e.type == 'blur' ||
                document.visibilityState != 'visible') {
                this.stop();
            } else if (!this.crashed) {
                this.tRex.reset();
                this.play();
            }
        },

        /**
         * Play a sound.
         * @param {SoundBuffer} soundBuffer
         */
        playSound: function (soundBuffer) {
            if (soundBuffer) {
                var sourceNode = this.audioContext.createBufferSource();
                sourceNode.buffer = soundBuffer;
                sourceNode.connect(this.audioContext.destination);
                sourceNode.start(0);
            }
        },

        /**
         * Inverts the current page / canvas colors.
         * @param {boolean} Whether to reset colors.
         */
        invert: function (reset) {
            if (reset) {
                document.body.classList.toggle(Runner.classes.INVERTED, false);
                this.invertTimer = 0;
                this.inverted = false;
            } else {
                this.inverted = document.body.classList.toggle(Runner.classes.INVERTED,
                    this.invertTrigger);
            }
        }
    };


    /**
     * Updates the canvas size taking into
     * account the backing store pixel ratio and
     * the device pixel ratio.
     *
     * See article by Paul Lewis:
     * http://www.html5rocks.com/en/tutorials/canvas/hidpi/
     *
     * @param {HTMLCanvasElement} canvas
     * @param {number} opt_width
     * @param {number} opt_height
     * @return {boolean} Whether the canvas was scaled.
     */
    Runner.updateCanvasScaling = function (canvas, opt_width, opt_height) {
        var context = canvas.getContext('2d');

        // Query the various pixel ratios
        var devicePixelRatio = Math.floor(window.devicePixelRatio) || 1;
        var backingStoreRatio = Math.floor(context.webkitBackingStorePixelRatio) || 1;
        var ratio = devicePixelRatio / backingStoreRatio;

        // Upscale the canvas if the two ratios don't match
        if (devicePixelRatio !== backingStoreRatio) {
            var oldWidth = opt_width || canvas.width;
            var oldHeight = opt_height || canvas.height;

            canvas.width = oldWidth * ratio;
            canvas.height = oldHeight * ratio;

            canvas.style.width = oldWidth + 'px';
            canvas.style.height = oldHeight + 'px';

            // Scale the context to counter the fact that we've manually scaled
            // our canvas element.
            context.scale(ratio, ratio);
            return true;
        } else if (devicePixelRatio == 1) {
            // Reset the canvas width / height. Fixes scaling bug when the page is
            // zoomed and the devicePixelRatio changes accordingly.
            canvas.style.width = canvas.width + 'px';
            canvas.style.height = canvas.height + 'px';
        }
        return false;
    };


    /**
     * Get random number.
     * @param {number} min
     * @param {number} max
     * @param {number}
     */
    function getRandomNum(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }


    /**
     * Vibrate on mobile devices.
     * @param {number} duration Duration of the vibration in milliseconds.
     */
    function vibrate(duration) {
        if (IS_MOBILE && window.navigator.vibrate) {
            window.navigator.vibrate(duration);
        }
    }


    /**
     * Create canvas element.
     * @param {HTMLElement} container Element to append canvas to.
     * @param {number} width
     * @param {number} height
     * @param {string} opt_classname
     * @return {HTMLCanvasElement}
     */
    function createCanvas(container, width, height, opt_classname) {
        var canvas = document.createElement('canvas');
        canvas.className = opt_classname ? Runner.classes.CANVAS + ' ' +
            opt_classname : Runner.classes.CANVAS;
        canvas.width = width;
        canvas.height = height;
        container.appendChild(canvas);

        return canvas;
    }


    /**
     * Decodes the base 64 audio to ArrayBuffer used by Web Audio.
     * @param {string} base64String
     */
    function decodeBase64ToArrayBuffer(base64String) {
        var len = (base64String.length / 4) * 3;
        var str = atob(base64String);
        var arrayBuffer = new ArrayBuffer(len);
        var bytes = new Uint8Array(arrayBuffer);

        for (var i = 0; i < len; i++) {
            bytes[i] = str.charCodeAt(i);
        }
        return bytes.buffer;
    }


    /**
     * Return the current timestamp.
     * @return {number}
     */
    function getTimeStamp() {
        return IS_IOS ? new Date().getTime() : performance.now();
    }


    //******************************************************************************


    /**
     * Game over panel.
     * @param {!HTMLCanvasElement} canvas
     * @param {Object} textImgPos
     * @param {Object} restartImgPos
     * @param {!Object} dimensions Canvas dimensions.
     * @constructor
     */
    function GameOverPanel(canvas, textImgPos, restartImgPos, dimensions) {
        this.canvas = canvas;
        this.canvasCtx = canvas.getContext('2d');
        this.canvasDimensions = dimensions;
        this.textImgPos = textImgPos;
        this.restartImgPos = restartImgPos;
        this.draw();
    };


    /**
     * Dimensions used in the panel.
     * @enum {number}
     */
    GameOverPanel.dimensions = {
        TEXT_X: 0,
        TEXT_Y: 13,
        TEXT_WIDTH: 191,
        TEXT_HEIGHT: 11,
        RESTART_WIDTH: 36,
        RESTART_HEIGHT: 32
    };


    GameOverPanel.prototype = {
        /**
         * Update the panel dimensions.
         * @param {number} width New canvas width.
         * @param {number} opt_height Optional new canvas height.
         */
        updateDimensions: function (width, opt_height) {
            this.canvasDimensions.WIDTH = width;
            if (opt_height) {
                this.canvasDimensions.HEIGHT = opt_height;
            }
        },

        /**
         * Draw the panel.
         */
        draw: function () {
            var dimensions = GameOverPanel.dimensions;

            var centerX = this.canvasDimensions.WIDTH / 2;

            // Game over text.
            var textSourceX = dimensions.TEXT_X;
            var textSourceY = dimensions.TEXT_Y;
            var textSourceWidth = dimensions.TEXT_WIDTH;
            var textSourceHeight = dimensions.TEXT_HEIGHT;

            var textTargetX = Math.round(centerX - (dimensions.TEXT_WIDTH / 2));
            var textTargetY = Math.round((this.canvasDimensions.HEIGHT - 25) / 3);
            var textTargetWidth = dimensions.TEXT_WIDTH;
            var textTargetHeight = dimensions.TEXT_HEIGHT;

            var restartSourceWidth = dimensions.RESTART_WIDTH;
            var restartSourceHeight = dimensions.RESTART_HEIGHT;
            var restartTargetX = centerX - (dimensions.RESTART_WIDTH / 2);
            var restartTargetY = this.canvasDimensions.HEIGHT / 2;

            if (IS_HIDPI) {
                textSourceY *= 2;
                textSourceX *= 2;
                textSourceWidth *= 2;
                textSourceHeight *= 2;
                restartSourceWidth *= 2;
                restartSourceHeight *= 2;
            }

            textSourceX += this.textImgPos.x;
            textSourceY += this.textImgPos.y;

            // Game over text from sprite.
            this.canvasCtx.drawImage(Runner.imageSprite,
                textSourceX, textSourceY, textSourceWidth, textSourceHeight,
                textTargetX, textTargetY, textTargetWidth, textTargetHeight);

            // Restart button.
            this.canvasCtx.drawImage(Runner.imageSprite,
                this.restartImgPos.x, this.restartImgPos.y,
                restartSourceWidth, restartSourceHeight,
                restartTargetX, restartTargetY, dimensions.RESTART_WIDTH,
                dimensions.RESTART_HEIGHT);
        }
    };


    //******************************************************************************

    /**
     * Check for a collision.
     * @param {!Obstacle} obstacle
     * @param {!Trex} tRex T-rex object.
     * @param {HTMLCanvasContext} opt_canvasCtx Optional canvas context for drawing
     *    collision boxes.
     * @return {Array<CollisionBox>}
     */
    function checkForCollision(obstacle, tRex, opt_canvasCtx) {
        var obstacleBoxXPos = Runner.defaultDimensions.WIDTH + obstacle.xPos;

        // Adjustments are made to the bounding box as there is a 1 pixel white
        // border around the t-rex and obstacles.
        var tRexBox = new CollisionBox(
            tRex.xPos + 1,
            tRex.yPos + 1,
            tRex.config.WIDTH - 2,
            tRex.config.HEIGHT - 2);

        var obstacleBox = new CollisionBox(
            obstacle.xPos + 1,
            obstacle.yPos + 1,
            obstacle.typeConfig.width * obstacle.size - 2,
            obstacle.typeConfig.height - 2);

        // Debug outer box
        if (opt_canvasCtx) {
            drawCollisionBoxes(opt_canvasCtx, tRexBox, obstacleBox);
        }

        // Simple outer bounds check.
        if (boxCompare(tRexBox, obstacleBox)) {
            var collisionBoxes = obstacle.collisionBoxes;
            var tRexCollisionBoxes = tRex.ducking ?
                Trex.collisionBoxes.DUCKING : Trex.collisionBoxes.RUNNING;

            // Detailed axis aligned box check.
            for (var t = 0; t < tRexCollisionBoxes.length; t++) {
                for (var i = 0; i < collisionBoxes.length; i++) {
                    // Adjust the box to actual positions.
                    var adjTrexBox =
                        createAdjustedCollisionBox(tRexCollisionBoxes[t], tRexBox);
                    var adjObstacleBox =
                        createAdjustedCollisionBox(collisionBoxes[i], obstacleBox);
                    var crashed = boxCompare(adjTrexBox, adjObstacleBox);

                    // Draw boxes for debug.
                    if (opt_canvasCtx) {
                        drawCollisionBoxes(opt_canvasCtx, adjTrexBox, adjObstacleBox);
                    }

                    if (crashed) {
                        return [adjTrexBox, adjObstacleBox];
                    }
                }
            }
        }
        return false;
    };


    /**
     * Adjust the collision box.
     * @param {!CollisionBox} box The original box.
     * @param {!CollisionBox} adjustment Adjustment box.
     * @return {CollisionBox} The adjusted collision box object.
     */
    function createAdjustedCollisionBox(box, adjustment) {
        return new CollisionBox(
            box.x + adjustment.x,
            box.y + adjustment.y,
            box.width,
            box.height);
    };


    /**
     * Draw the collision boxes for debug.
     */
    function drawCollisionBoxes(canvasCtx, tRexBox, obstacleBox) {
        canvasCtx.save();
        canvasCtx.strokeStyle = '#f00';
        canvasCtx.strokeRect(tRexBox.x, tRexBox.y, tRexBox.width, tRexBox.height);

        canvasCtx.strokeStyle = '#0f0';
        canvasCtx.strokeRect(obstacleBox.x, obstacleBox.y,
            obstacleBox.width, obstacleBox.height);
        canvasCtx.restore();
    };


    /**
     * Compare two collision boxes for a collision.
     * @param {CollisionBox} tRexBox
     * @param {CollisionBox} obstacleBox
     * @return {boolean} Whether the boxes intersected.
     */
    function boxCompare(tRexBox, obstacleBox) {
        var crashed = false;
        var tRexBoxX = tRexBox.x;
        var tRexBoxY = tRexBox.y;

        var obstacleBoxX = obstacleBox.x;
        var obstacleBoxY = obstacleBox.y;

        // Axis-Aligned Bounding Box method.
        if (tRexBox.x < obstacleBoxX + obstacleBox.width &&
            tRexBox.x + tRexBox.width > obstacleBoxX &&
            tRexBox.y < obstacleBox.y + obstacleBox.height &&
            tRexBox.height + tRexBox.y > obstacleBox.y) {
            crashed = true;
        }

        return crashed;
    };


    //******************************************************************************

    /**
     * Collision box object.
     * @param {number} x X position.
     * @param {number} y Y Position.
     * @param {number} w Width.
     * @param {number} h Height.
     */
    function CollisionBox(x, y, w, h) {
        this.x = x;
        this.y = y;
        this.width = w;
        this.height = h;
    };


    //******************************************************************************

    /**
     * Obstacle.
     * @param {HTMLCanvasCtx} canvasCtx
     * @param {Obstacle.type} type
     * @param {Object} spritePos Obstacle position in sprite.
     * @param {Object} dimensions
     * @param {number} gapCoefficient Mutipler in determining the gap.
     * @param {number} speed
     * @param {number} opt_xOffset
     */
    function Obstacle(canvasCtx, type, spriteImgPos, dimensions,
        gapCoefficient, speed, opt_xOffset) {

        this.canvasCtx = canvasCtx;
        this.spritePos = spriteImgPos;
        this.typeConfig = type;
        this.gapCoefficient = gapCoefficient;
        this.size = getRandomNum(1, Obstacle.MAX_OBSTACLE_LENGTH);
        this.dimensions = dimensions;
        this.remove = false;
        this.xPos = dimensions.WIDTH + (opt_xOffset || 0);
        this.yPos = 0;
        this.width = 0;
        this.collisionBoxes = [];
        this.gap = 0;
        this.speedOffset = 0;

        // For animated obstacles.
        this.currentFrame = 0;
        this.timer = 0;

        this.init(speed);
    };

    /**
     * Coefficient for calculating the maximum gap.
     * @const
     */
    Obstacle.MAX_GAP_COEFFICIENT = 1.5;

    /**
     * Maximum obstacle grouping count.
     * @const
     */
    Obstacle.MAX_OBSTACLE_LENGTH = 3,


        Obstacle.prototype = {
            /**
             * Initialise the DOM for the obstacle.
             * @param {number} speed
             */
            init: function (speed) {
                this.cloneCollisionBoxes();

                // Only allow sizing if we're at the right speed.
                if (this.size > 1 && this.typeConfig.multipleSpeed > speed) {
                    this.size = 1;
                }

                this.width = this.typeConfig.width * this.size;

                // Check if obstacle can be positioned at various heights.
                if (Array.isArray(this.typeConfig.yPos)) {
                    var yPosConfig = IS_MOBILE ? this.typeConfig.yPosMobile :
                        this.typeConfig.yPos;
                    this.yPos = yPosConfig[getRandomNum(0, yPosConfig.length - 1)];
                } else {
                    this.yPos = this.typeConfig.yPos;
                }

                this.draw();

                // Make collision box adjustments,
                // Central box is adjusted to the size as one box.
                //      ____        ______        ________
                //    _|   |-|    _|     |-|    _|       |-|
                //   | |<->| |   | |<--->| |   | |<----->| |
                //   | | 1 | |   | |  2  | |   | |   3   | |
                //   |_|___|_|   |_|_____|_|   |_|_______|_|
                //
                if (this.size > 1) {
                    this.collisionBoxes[1].width = this.width - this.collisionBoxes[0].width -
                        this.collisionBoxes[2].width;
                    this.collisionBoxes[2].x = this.width - this.collisionBoxes[2].width;
                }

                // For obstacles that go at a different speed from the horizon.
                if (this.typeConfig.speedOffset) {
                    this.speedOffset = Math.random() > 0.5 ? this.typeConfig.speedOffset :
                        -this.typeConfig.speedOffset;
                }

                this.gap = this.getGap(this.gapCoefficient, speed);
            },

            /**
             * Draw and crop based on size.
             */
            draw: function () {
                var sourceWidth = this.typeConfig.width;
                var sourceHeight = this.typeConfig.height;

                if (IS_HIDPI) {
                    sourceWidth = sourceWidth * 2;
                    sourceHeight = sourceHeight * 2;
                }

                // X position in sprite.
                var sourceX = (sourceWidth * this.size) * (0.5 * (this.size - 1)) +
                    this.spritePos.x;

                // Animation frames.
                if (this.currentFrame > 0) {
                    sourceX += sourceWidth * this.currentFrame;
                }

                this.canvasCtx.drawImage(Runner.imageSprite,
                    sourceX, this.spritePos.y,
                    sourceWidth * this.size, sourceHeight,
                    this.xPos, this.yPos,
                    this.typeConfig.width * this.size, this.typeConfig.height);
            },

            /**
             * Obstacle frame update.
             * @param {number} deltaTime
             * @param {number} speed
             */
            update: function (deltaTime, speed) {
                if (!this.remove) {
                    if (this.typeConfig.speedOffset) {
                        speed += this.speedOffset;
                    }
                    this.xPos -= Math.floor((speed * FPS / 1000) * deltaTime);

                    // Update frame
                    if (this.typeConfig.numFrames) {
                        this.timer += deltaTime;
                        if (this.timer >= this.typeConfig.frameRate) {
                            this.currentFrame =
                                this.currentFrame == this.typeConfig.numFrames - 1 ?
                                    0 : this.currentFrame + 1;
                            this.timer = 0;
                        }
                    }
                    this.draw();

                    if (!this.isVisible()) {
                        this.remove = true;
                    }
                }
            },

            /**
             * Calculate a random gap size.
             * - Minimum gap gets wider as speed increses
             * @param {number} gapCoefficient
             * @param {number} speed
             * @return {number} The gap size.
             */
            getGap: function (gapCoefficient, speed) {
                var minGap = Math.round(this.width * speed +
                    this.typeConfig.minGap * gapCoefficient);
                var maxGap = Math.round(minGap * Obstacle.MAX_GAP_COEFFICIENT);
                return getRandomNum(minGap, maxGap);
            },

            /**
             * Check if obstacle is visible.
             * @return {boolean} Whether the obstacle is in the game area.
             */
            isVisible: function () {
                return this.xPos + this.width > 0;
            },

            /**
             * Make a copy of the collision boxes, since these will change based on
             * obstacle type and size.
             */
            cloneCollisionBoxes: function () {
                var collisionBoxes = this.typeConfig.collisionBoxes;

                for (var i = collisionBoxes.length - 1; i >= 0; i--) {
                    this.collisionBoxes[i] = new CollisionBox(collisionBoxes[i].x,
                        collisionBoxes[i].y, collisionBoxes[i].width,
                        collisionBoxes[i].height);
                }
            }
        };


    /**
     * Obstacle definitions.
     * minGap: minimum pixel space betweeen obstacles.
     * multipleSpeed: Speed at which multiples are allowed.
     * speedOffset: speed faster / slower than the horizon.
     * minSpeed: Minimum speed which the obstacle can make an appearance.
     */
    Obstacle.types = [
        {
            type: 'CACTUS_SMALL',
            width: 17,
            height: 35,
            yPos: 105,
            multipleSpeed: 4,
            minGap: 120,
            minSpeed: 0,
            collisionBoxes: [
                new CollisionBox(0, 7, 5, 27),
                new CollisionBox(4, 0, 6, 34),
                new CollisionBox(10, 4, 7, 14)
            ]
        },
        {
            type: 'CACTUS_LARGE',
            width: 25,
            height: 50,
            yPos: 90,
            multipleSpeed: 7,
            minGap: 120,
            minSpeed: 0,
            collisionBoxes: [
                new CollisionBox(0, 12, 7, 38),
                new CollisionBox(8, 0, 7, 49),
                new CollisionBox(13, 10, 10, 38)
            ]
        },
        {
            type: 'PTERODACTYL',
            width: 46,
            height: 40,
            yPos: [100, 75, 50], // Variable height.
            yPosMobile: [100, 50], // Variable height mobile.
            multipleSpeed: 999,
            minSpeed: 8.5,
            minGap: 150,
            collisionBoxes: [
                new CollisionBox(15, 15, 16, 5),
                new CollisionBox(18, 21, 24, 6),
                new CollisionBox(2, 14, 4, 3),
                new CollisionBox(6, 10, 4, 7),
                new CollisionBox(10, 8, 6, 9)
            ],
            numFrames: 2,
            frameRate: 1000 / 6,
            speedOffset: .8
        }
    ];


    //******************************************************************************
    /**
     * T-rex game character.
     * @param {HTMLCanvas} canvas
     * @param {Object} spritePos Positioning within image sprite.
     * @constructor
     */
    function Trex(canvas, spritePos) {
        this.canvas = canvas;
        this.canvasCtx = canvas.getContext('2d');
        this.spritePos = spritePos;
        this.xPos = 0;
        this.yPos = 0;
        // Position when on the ground.
        this.groundYPos = 0;
        this.currentFrame = 0;
        this.currentAnimFrames = [];
        this.blinkDelay = 0;
        this.blinkCount = 0;
        this.animStartTime = 0;
        this.timer = 0;
        this.msPerFrame = 1000 / FPS;
        this.config = Trex.config;
        // Current status.
        this.status = Trex.status.WAITING;

        this.jumping = false;
        this.ducking = false;
        this.jumpVelocity = 0;
        this.reachedMinHeight = false;
        this.speedDrop = false;
        this.jumpCount = 0;
        this.jumpspotX = 0;

        this.init();
    };


    /**
     * T-rex player config.
     * @enum {number}
     */
    Trex.config = {
        DROP_VELOCITY: -5,
        GRAVITY: 0.6,
        HEIGHT: 54,
        HEIGHT_DUCK: 42,
        INIITAL_JUMP_VELOCITY: -10,
        INTRO_DURATION: 1500,
        MAX_JUMP_HEIGHT: 30,
        MIN_JUMP_HEIGHT: 30,
        SPEED_DROP_COEFFICIENT: 3,
        SPRITE_WIDTH: 262,
        START_X_POS: 50,
        WIDTH: 38,
        WIDTH_DUCK: 58
    };


    /**
     * Used in collision detection.
     * @type {Array<CollisionBox>}
     */
    Trex.collisionBoxes = {
        DUCKING: [
            new CollisionBox(1, 18, 55, 25)
        ],
        RUNNING: [
            new CollisionBox(22, 0, 17, 16),
            new CollisionBox(1, 18, 30, 9),
            new CollisionBox(10, 35, 14, 8),
            new CollisionBox(1, 24, 29, 5),
            new CollisionBox(5, 30, 21, 4),
            new CollisionBox(9, 34, 15, 4)
        ]
    };


    /**
     * Animation states.
     * @enum {string}
     */
    Trex.status = {
        CRASHED: 'CRASHED',
        DUCKING: 'DUCKING',
        JUMPING: 'JUMPING',
        RUNNING: 'RUNNING',
        WAITING: 'WAITING'
    };

    /**
     * Blinking coefficient.
     * @const
     */
    Trex.BLINK_TIMING = 7000;


    /**
     * Animation config for different states.
     * @enum {Object}
     */
    Trex.animFrames = {
        WAITING: {
            frames: [36, 0],
            msPerFrame: 1000 / 3
        },
        RUNNING: {
            frames: [78, 116],
            msPerFrame: 1000 / 12
        },
        CRASHED: {
            frames: [154],
            msPerFrame: 1000 / 60
        },
        JUMPING: {
            frames: [0],
            msPerFrame: 1000 / 60
        },
        DUCKING: {
            frames: [244, 302],
            msPerFrame: 1000 / 8
        }
    };


    Trex.prototype = {
        /**
         * T-rex player initaliser.
         * Sets the t-rex to blink at random intervals.
         */
        init: function () {
            this.groundYPos = Runner.defaultDimensions.HEIGHT - this.config.HEIGHT -
                Runner.config.BOTTOM_PAD;
            this.yPos = this.groundYPos;
            this.minJumpHeight = this.groundYPos - this.config.MIN_JUMP_HEIGHT;

            this.draw(0, 0);
            this.update(0, Trex.status.WAITING);
        },

        /**
         * Setter for the jump velocity.
         * The approriate drop velocity is also set.
         */
        setJumpVelocity: function (setting) {
            this.config.INIITAL_JUMP_VELOCITY = -setting;
            this.config.DROP_VELOCITY = -setting / 2;
        },

        /**
         * Set the animation status.
         * @param {!number} deltaTime
         * @param {Trex.status} status Optional status to switch to.
         */
        update: function (deltaTime, opt_status) {
            this.timer += deltaTime;

            // Update the status.
            if (opt_status) {
                this.status = opt_status;
                this.currentFrame = 0;
                this.msPerFrame = Trex.animFrames[opt_status].msPerFrame;
                this.currentAnimFrames = Trex.animFrames[opt_status].frames;

                if (opt_status == Trex.status.WAITING) {
                    this.animStartTime = getTimeStamp();
                    this.setBlinkDelay();
                }
            }

            // Game intro animation, T-rex moves in from the left.
            if (this.playingIntro && this.xPos < this.config.START_X_POS) {
                this.xPos += Math.round((this.config.START_X_POS /
                    this.config.INTRO_DURATION) * deltaTime);
            }

            if (this.status == Trex.status.WAITING) {
                this.blink(getTimeStamp());
            } else {
                this.draw(this.currentAnimFrames[this.currentFrame], 0);
            }

            // Update the frame position.
            if (this.timer >= this.msPerFrame) {
                this.currentFrame = this.currentFrame ==
                    this.currentAnimFrames.length - 1 ? 0 : this.currentFrame + 1;
                this.timer = 0;
            }

            // Speed drop becomes duck if the down key is still being pressed.
            if (this.speedDrop && this.yPos == this.groundYPos) {
                this.speedDrop = false;
                this.setDuck(true);
            }
        },

        /**
         * Draw the t-rex to a particular position.
         * @param {number} x
         * @param {number} y
         */
        draw: function (x, y) {
            var sourceX = x;
            var sourceY = y;
            var sourceWidth = this.ducking && this.status != Trex.status.CRASHED ?
                this.config.WIDTH_DUCK : this.config.WIDTH;
            var sourceHeight = this.config.HEIGHT;

            if (IS_HIDPI) {
                sourceX *= 2;
                sourceY *= 2;
                sourceWidth *= 2;
                sourceHeight *= 2;
            }

            // Adjustments for sprite sheet position.
            sourceX += this.spritePos.x;
            sourceY += this.spritePos.y;

            // Ducking.
            if (this.ducking && this.status != Trex.status.CRASHED) {
                this.canvasCtx.drawImage(Runner.imageSprite, sourceX, sourceY,
                    sourceWidth, sourceHeight,
                    this.xPos, this.yPos,
                    this.config.WIDTH_DUCK, this.config.HEIGHT);
            } else {
                // Crashed whilst ducking. Trex is standing up so needs adjustment.
                if (this.ducking && this.status == Trex.status.CRASHED) {
                    this.xPos++;
                }
                // Standing / running
                this.canvasCtx.drawImage(Runner.imageSprite, sourceX, sourceY,
                    sourceWidth, sourceHeight,
                    this.xPos, this.yPos,
                    this.config.WIDTH, this.config.HEIGHT);
            }
        },

        /**
         * Sets a random time for the blink to happen.
         */
        setBlinkDelay: function () {
            this.blinkDelay = Math.ceil(Math.random() * Trex.BLINK_TIMING);
        },

        /**
         * Make t-rex blink at random intervals.
         * @param {number} time Current time in milliseconds.
         */
        blink: function (time) {
            var deltaTime = time - this.animStartTime;

            if (deltaTime >= this.blinkDelay) {
                this.draw(this.currentAnimFrames[this.currentFrame], 0);

                if (this.currentFrame == 1) {
                    // Set new random delay to blink.
                    this.setBlinkDelay();
                    this.animStartTime = time;
                    this.blinkCount++;
                }
            }
        },

        /**
         * Initialise a jump.
         * @param {number} speed
         */
        startJump: function (speed) {
            if (!this.jumping) {
                this.update(0, Trex.status.JUMPING);
                // Tweak the jump velocity based on the speed.
                this.jumpVelocity = this.config.INIITAL_JUMP_VELOCITY - (speed / 10);
                this.jumping = true;
                this.reachedMinHeight = false;
                this.speedDrop = false;
            }
        },

        /**
         * Jump is complete, falling down.
         */
        endJump: function () {
            if (this.reachedMinHeight &&
                this.jumpVelocity < this.config.DROP_VELOCITY) {
                this.jumpVelocity = this.config.DROP_VELOCITY;
            }
        },

        /**
         * Update frame for a jump.
         * @param {number} deltaTime
         * @param {number} speed
         */
        updateJump: function (deltaTime, speed) {
            var msPerFrame = Trex.animFrames[this.status].msPerFrame;
            var framesElapsed = deltaTime / msPerFrame;

            // Speed drop makes Trex fall faster.
            if (this.speedDrop) {
                this.yPos += Math.round(this.jumpVelocity *
                    this.config.SPEED_DROP_COEFFICIENT * framesElapsed);
            } else {
                this.yPos += Math.round(this.jumpVelocity * framesElapsed);
            }

            this.jumpVelocity += this.config.GRAVITY * framesElapsed;

            // Minimum height has been reached.
            if (this.yPos < this.minJumpHeight || this.speedDrop) {
                this.reachedMinHeight = true;
            }

            // Reached max height
            if (this.yPos < this.config.MAX_JUMP_HEIGHT || this.speedDrop) {
                this.endJump();
            }

            // Back down at ground level. Jump completed.
            if (this.yPos > this.groundYPos) {
                this.reset();
                this.jumpCount++;
            }

            this.update(deltaTime);
        },

        /**
         * Set the speed drop. Immediately cancels the current jump.
         */
        setSpeedDrop: function () {
            this.speedDrop = true;
            this.jumpVelocity = 1;
        },

        /**
         * @param {boolean} isDucking.
         */
        setDuck: function (isDucking) {
            if (isDucking && this.status != Trex.status.DUCKING) {
                this.update(0, Trex.status.DUCKING);
                this.ducking = true;
            } else if (this.status == Trex.status.DUCKING) {
                this.update(0, Trex.status.RUNNING);
                this.ducking = false;
            }
        },

        /**
         * Reset the t-rex to running at start of game.
         */
        reset: function () {
            this.yPos = this.groundYPos;
            this.jumpVelocity = 0;
            this.jumping = false;
            this.ducking = false;
            this.update(0, Trex.status.RUNNING);
            this.midair = false;
            this.speedDrop = false;
            this.jumpCount = 0;
        }
    };


    //******************************************************************************

    /**
     * Handles displaying the distance meter.
     * @param {!HTMLCanvasElement} canvas
     * @param {Object} spritePos Image position in sprite.
     * @param {number} canvasWidth
     * @constructor
     */
    function DistanceMeter(canvas, spritePos, canvasWidth) {
        this.canvas = canvas;
        this.canvasCtx = canvas.getContext('2d');
        this.image = Runner.imageSprite;
        this.spritePos = spritePos;
        this.x = 0;
        this.y = 5;

        this.currentDistance = 0;
        this.maxScore = 0;
        this.highScore = 0;
        this.container = null;

        this.digits = [];
        this.acheivement = false;
        this.defaultString = '';
        this.flashTimer = 0;
        this.flashIterations = 0;
        this.invertTrigger = false;

        this.config = DistanceMeter.config;
        this.maxScoreUnits = this.config.MAX_DISTANCE_UNITS;
        this.init(canvasWidth);
    };


    /**
     * @enum {number}
     */
    DistanceMeter.dimensions = {
        WIDTH: 10,
        HEIGHT: 13,
        DEST_WIDTH: 11
    };


    /**
     * Y positioning of the digits in the sprite sheet.
     * X position is always 0.
     * @type {Array<number>}
     */
    DistanceMeter.yPos = [0, 13, 27, 40, 53, 67, 80, 93, 107, 120];


    /**
     * Distance meter config.
     * @enum {number}
     */
    DistanceMeter.config = {
        // Number of digits.
        MAX_DISTANCE_UNITS: 5,

        // Distance that causes achievement animation.
        ACHIEVEMENT_DISTANCE: 100,

        // Used for conversion from pixel distance to a scaled unit.
        COEFFICIENT: 0.025,

        // Flash duration in milliseconds.
        FLASH_DURATION: 1000 / 4,

        // Flash iterations for achievement animation.
        FLASH_ITERATIONS: 3
    };


    DistanceMeter.prototype = {
        /**
         * Initialise the distance meter to '00000'.
         * @param {number} width Canvas width in px.
         */
        init: function (width) {
            var maxDistanceStr = '';

            this.calcXPos(width);
            this.maxScore = this.maxScoreUnits;
            for (var i = 0; i < this.maxScoreUnits; i++) {
                this.draw(i, 0);
                this.defaultString += '0';
                maxDistanceStr += '9';
            }

            this.maxScore = parseInt(maxDistanceStr);
        },

        /**
         * Calculate the xPos in the canvas.
         * @param {number} canvasWidth
         */
        calcXPos: function (canvasWidth) {
            this.x = canvasWidth - (DistanceMeter.dimensions.DEST_WIDTH *
                (this.maxScoreUnits + 1));
        },

        /**
         * Draw a digit to canvas.
         * @param {number} digitPos Position of the digit.
         * @param {number} value Digit value 0-9.
         * @param {boolean} opt_highScore Whether drawing the high score.
         */
        draw: function (digitPos, value, opt_highScore) {
            var sourceWidth = DistanceMeter.dimensions.WIDTH;
            var sourceHeight = DistanceMeter.dimensions.HEIGHT;
            var sourceX = DistanceMeter.dimensions.WIDTH * value;
            var sourceY = 0;

            var targetX = digitPos * DistanceMeter.dimensions.DEST_WIDTH;
            var targetY = this.y;
            var targetWidth = DistanceMeter.dimensions.WIDTH;
            var targetHeight = DistanceMeter.dimensions.HEIGHT;

            // For high DPI we 2x source values.
            if (IS_HIDPI) {
                sourceWidth *= 2;
                sourceHeight *= 2;
                sourceX *= 2;
            }

            sourceX += this.spritePos.x;
            sourceY += this.spritePos.y;

            this.canvasCtx.save();

            if (opt_highScore) {
                // Left of the current score.
                var highScoreX = this.x - (this.maxScoreUnits * 2) *
                    DistanceMeter.dimensions.WIDTH;
                this.canvasCtx.translate(highScoreX, this.y);
            } else {
                this.canvasCtx.translate(this.x, this.y);
            }

            this.canvasCtx.drawImage(this.image, sourceX, sourceY,
                sourceWidth, sourceHeight,
                targetX, targetY,
                targetWidth, targetHeight
            );

            this.canvasCtx.restore();
        },

        /**
         * Covert pixel distance to a 'real' distance.
         * @param {number} distance Pixel distance ran.
         * @return {number} The 'real' distance ran.
         */
        getActualDistance: function (distance) {
            return distance ? Math.round(distance * this.config.COEFFICIENT) : 0;
        },

        /**
         * Update the distance meter.
         * @param {number} distance
         * @param {number} deltaTime
         * @return {boolean} Whether the acheivement sound fx should be played.
         */
        update: function (deltaTime, distance) {
            var paint = true;
            var playSound = false;

            if (!this.acheivement) {
                distance = this.getActualDistance(distance);
                // Score has gone beyond the initial digit count.
                if (distance > this.maxScore && this.maxScoreUnits ==
                    this.config.MAX_DISTANCE_UNITS) {
                    this.maxScoreUnits++;
                    this.maxScore = parseInt(this.maxScore + '9');
                } else {
                    this.distance = 0;
                }

                if (distance > 0) {
                    // Acheivement unlocked
                    if (distance % this.config.ACHIEVEMENT_DISTANCE == 0) {
                        // Flash score and play sound.
                        this.acheivement = true;
                        this.flashTimer = 0;
                        playSound = true;
                    }

                    // Create a string representation of the distance with leading 0.
                    var distanceStr = (this.defaultString +
                        distance).substr(-this.maxScoreUnits);
                    this.digits = distanceStr.split('');
                } else {
                    this.digits = this.defaultString.split('');
                }
            } else {
                // Control flashing of the score on reaching acheivement.
                if (this.flashIterations <= this.config.FLASH_ITERATIONS) {
                    this.flashTimer += deltaTime;

                    if (this.flashTimer < this.config.FLASH_DURATION) {
                        paint = false;
                    } else if (this.flashTimer >
                        this.config.FLASH_DURATION * 2) {
                        this.flashTimer = 0;
                        this.flashIterations++;
                    }
                } else {
                    this.acheivement = false;
                    this.flashIterations = 0;
                    this.flashTimer = 0;
                }
            }

            // Draw the digits if not flashing.
            if (paint) {
                for (var i = this.digits.length - 1; i >= 0; i--) {
                    this.draw(i, parseInt(this.digits[i]));
                }
            }

            this.drawHighScore();
            return playSound;
        },

        /**
         * Draw the high score.
         */
        drawHighScore: function () {
            this.canvasCtx.save();
            this.canvasCtx.globalAlpha = .8;
            for (var i = this.highScore.length - 1; i >= 0; i--) {
                this.draw(i, parseInt(this.highScore[i], 10), true);
            }
            this.canvasCtx.restore();
        },

        /**
         * Set the highscore as a array string.
         * Position of char in the sprite: H - 10, I - 11.
         * @param {number} distance Distance ran in pixels.
         */
        setHighScore: function (distance) {
            distance = this.getActualDistance(distance);
            var highScoreStr = (this.defaultString +
                distance).substr(-this.maxScoreUnits);

            this.highScore = ['10', '11', ''].concat(highScoreStr.split(''));
        },

        /**
         * Reset the distance meter back to '00000'.
         */
        reset: function () {
            this.update(0);
            this.acheivement = false;
        }
    };


    //******************************************************************************

    /**
     * Cloud background item.
     * Similar to an obstacle object but without collision boxes.
     * @param {HTMLCanvasElement} canvas Canvas element.
     * @param {Object} spritePos Position of image in sprite.
     * @param {number} containerWidth
     */
    function Cloud(canvas, spritePos, containerWidth) {
        this.canvas = canvas;
        this.canvasCtx = this.canvas.getContext('2d');
        this.spritePos = spritePos;
        this.containerWidth = containerWidth;
        this.xPos = containerWidth;
        this.yPos = 0;
        this.remove = false;
        this.cloudGap = getRandomNum(Cloud.config.MIN_CLOUD_GAP,
            Cloud.config.MAX_CLOUD_GAP);

        this.init();
    };


    /**
     * Cloud object config.
     * @enum {number}
     */
    Cloud.config = {
        HEIGHT: 14,
        MAX_CLOUD_GAP: 400,
        MAX_SKY_LEVEL: 30,
        MIN_CLOUD_GAP: 100,
        MIN_SKY_LEVEL: 71,
        WIDTH: 46
    };


    Cloud.prototype = {
        /**
         * Initialise the cloud. Sets the Cloud height.
         */
        init: function () {
            this.yPos = getRandomNum(Cloud.config.MAX_SKY_LEVEL,
                Cloud.config.MIN_SKY_LEVEL);
            this.draw();
        },

        /**
         * Draw the cloud.
         */
        draw: function () {
            this.canvasCtx.save();
            var sourceWidth = Cloud.config.WIDTH;
            var sourceHeight = Cloud.config.HEIGHT;

            if (IS_HIDPI) {
                sourceWidth = sourceWidth * 2;
                sourceHeight = sourceHeight * 2;
            }

            this.canvasCtx.drawImage(Runner.imageSprite, this.spritePos.x,
                this.spritePos.y,
                sourceWidth, sourceHeight,
                this.xPos, this.yPos,
                Cloud.config.WIDTH, Cloud.config.HEIGHT);

            this.canvasCtx.restore();
        },

        /**
         * Update the cloud position.
         * @param {number} speed
         */
        update: function (speed) {
            if (!this.remove) {
                this.xPos -= Math.ceil(speed);
                this.draw();

                // Mark as removeable if no longer in the canvas.
                if (!this.isVisible()) {
                    this.remove = true;
                }
            }
        },

        /**
         * Check if the cloud is visible on the stage.
         * @return {boolean}
         */
        isVisible: function () {
            return this.xPos + Cloud.config.WIDTH > 0;
        }
    };


    //******************************************************************************

    /**
     * Nightmode shows a moon and stars on the horizon.
     */
    function NightMode(canvas, spritePos, containerWidth) {
        this.spritePos = spritePos;
        this.canvas = canvas;
        this.canvasCtx = canvas.getContext('2d');
        this.xPos = containerWidth - 50;
        this.yPos = 30;
        this.currentPhase = 0;
        this.opacity = 0;
        this.containerWidth = containerWidth;
        this.stars = [];
        this.drawStars = false;
        this.placeStars();
    };

    /**
     * @enum {number}
     */
    NightMode.config = {
        FADE_SPEED: 0.035,
        HEIGHT: 40,
        MOON_SPEED: 0.25,
        NUM_STARS: 2,
        STAR_SIZE: 9,
        STAR_SPEED: 0.3,
        STAR_MAX_Y: 70,
        WIDTH: 20
    };

    NightMode.phases = [140, 120, 100, 60, 40, 20, 0];

    NightMode.prototype = {
        /**
         * Update moving moon, changing phases.
         * @param {boolean} activated Whether night mode is activated.
         * @param {number} delta
         */
        update: function (activated, delta) {
            // Moon phase.
            if (activated && this.opacity == 0) {
                this.currentPhase++;

                if (this.currentPhase >= NightMode.phases.length) {
                    this.currentPhase = 0;
                }
            }

            // Fade in / out.
            if (activated && (this.opacity < 1 || this.opacity == 0)) {
                this.opacity += NightMode.config.FADE_SPEED;
            } else if (this.opacity > 0) {
                this.opacity -= NightMode.config.FADE_SPEED;
            }

            // Set moon positioning.
            if (this.opacity > 0) {
                this.xPos = this.updateXPos(this.xPos, NightMode.config.MOON_SPEED);

                // Update stars.
                if (this.drawStars) {
                    for (var i = 0; i < NightMode.config.NUM_STARS; i++) {
                        this.stars[i].x = this.updateXPos(this.stars[i].x,
                            NightMode.config.STAR_SPEED);
                    }
                }
                this.draw();
            } else {
                this.opacity = 0;
                this.placeStars();
            }
            this.drawStars = true;
        },

        updateXPos: function (currentPos, speed) {
            if (currentPos < -NightMode.config.WIDTH) {
                currentPos = this.containerWidth;
            } else {
                currentPos -= speed;
            }
            return currentPos;
        },

        draw: function () {
            var moonSourceWidth = this.currentPhase == 3 ? NightMode.config.WIDTH * 2 :
                NightMode.config.WIDTH;
            var moonSourceHeight = NightMode.config.HEIGHT;
            var moonSourceX = this.spritePos.x + NightMode.phases[this.currentPhase];
            var moonOutputWidth = moonSourceWidth;
            var starSize = NightMode.config.STAR_SIZE;
            var starSourceX = Runner.spriteDefinition.LDPI.STAR.x;

            if (IS_HIDPI) {
                moonSourceWidth *= 2;
                moonSourceHeight *= 2;
                moonSourceX = this.spritePos.x +
                    (NightMode.phases[this.currentPhase] * 2);
                starSize *= 2;
                starSourceX = Runner.spriteDefinition.HDPI.STAR.x;
            }

            this.canvasCtx.save();
            this.canvasCtx.globalAlpha = this.opacity;

            // Stars.
            if (this.drawStars) {
                for (var i = 0; i < NightMode.config.NUM_STARS; i++) {
                    this.canvasCtx.drawImage(Runner.imageSprite,
                        starSourceX, this.stars[i].sourceY, starSize, starSize,
                        Math.round(this.stars[i].x), this.stars[i].y,
                        NightMode.config.STAR_SIZE, NightMode.config.STAR_SIZE);
                }
            }

            // Moon.
            this.canvasCtx.drawImage(Runner.imageSprite, moonSourceX,
                this.spritePos.y, moonSourceWidth, moonSourceHeight,
                Math.round(this.xPos), this.yPos,
                moonOutputWidth, NightMode.config.HEIGHT);

            this.canvasCtx.globalAlpha = 1;
            this.canvasCtx.restore();
        },

        // Do star placement.
        placeStars: function () {
            var segmentSize = Math.round(this.containerWidth /
                NightMode.config.NUM_STARS);

            for (var i = 0; i < NightMode.config.NUM_STARS; i++) {
                this.stars[i] = {};
                this.stars[i].x = getRandomNum(segmentSize * i, segmentSize * (i + 1));
                this.stars[i].y = getRandomNum(0, NightMode.config.STAR_MAX_Y);

                if (IS_HIDPI) {
                    this.stars[i].sourceY = Runner.spriteDefinition.HDPI.STAR.y +
                        NightMode.config.STAR_SIZE * 2 * i;
                } else {
                    this.stars[i].sourceY = Runner.spriteDefinition.LDPI.STAR.y +
                        NightMode.config.STAR_SIZE * i;
                }
            }
        },

        reset: function () {
            this.currentPhase = 0;
            this.opacity = 0;
            this.update(false);
        }

    };


    //******************************************************************************

    /**
     * Horizon Line.
     * Consists of two connecting lines. Randomly assigns a flat / bumpy horizon.
     * @param {HTMLCanvasElement} canvas
     * @param {Object} spritePos Horizon position in sprite.
     * @constructor
     */
    function HorizonLine(canvas, spritePos) {
        this.spritePos = spritePos;
        this.canvas = canvas;
        this.canvasCtx = canvas.getContext('2d');
        this.sourceDimensions = {};
        this.dimensions = HorizonLine.dimensions;
        this.sourceXPos = [this.spritePos.x, this.spritePos.x +
            this.dimensions.WIDTH];
        this.xPos = [];
        this.yPos = 0;
        this.bumpThreshold = 0.5;

        this.setSourceDimensions();
        this.draw();
    };


    /**
     * Horizon line dimensions.
     * @enum {number}
     */
    HorizonLine.dimensions = {
        WIDTH: 600,
        HEIGHT: 12,
        YPOS: 127
    };


    HorizonLine.prototype = {
        /**
         * Set the source dimensions of the horizon line.
         */
        setSourceDimensions: function () {

            for (var dimension in HorizonLine.dimensions) {
                if (IS_HIDPI) {
                    if (dimension != 'YPOS') {
                        this.sourceDimensions[dimension] =
                            HorizonLine.dimensions[dimension] * 2;
                    }
                } else {
                    this.sourceDimensions[dimension] =
                        HorizonLine.dimensions[dimension];
                }
                this.dimensions[dimension] = HorizonLine.dimensions[dimension];
            }

            this.xPos = [0, HorizonLine.dimensions.WIDTH];
            this.yPos = HorizonLine.dimensions.YPOS;
        },

        /**
         * Return the crop x position of a type.
         */
        getRandomType: function () {
            return Math.random() > this.bumpThreshold ? this.dimensions.WIDTH : 0;
        },

        /**
         * Draw the horizon line.
         */
        draw: function () {
            this.canvasCtx.drawImage(Runner.imageSprite, this.sourceXPos[0],
                this.spritePos.y,
                this.sourceDimensions.WIDTH, this.sourceDimensions.HEIGHT,
                this.xPos[0], this.yPos,
                this.dimensions.WIDTH, this.dimensions.HEIGHT);

            this.canvasCtx.drawImage(Runner.imageSprite, this.sourceXPos[1],
                this.spritePos.y,
                this.sourceDimensions.WIDTH, this.sourceDimensions.HEIGHT,
                this.xPos[1], this.yPos,
                this.dimensions.WIDTH, this.dimensions.HEIGHT);
        },

        /**
         * Update the x position of an indivdual piece of the line.
         * @param {number} pos Line position.
         * @param {number} increment
         */
        updateXPos: function (pos, increment) {
            var line1 = pos;
            var line2 = pos == 0 ? 1 : 0;

            this.xPos[line1] -= increment;
            this.xPos[line2] = this.xPos[line1] + this.dimensions.WIDTH;

            if (this.xPos[line1] <= -this.dimensions.WIDTH) {
                this.xPos[line1] += this.dimensions.WIDTH * 2;
                this.xPos[line2] = this.xPos[line1] - this.dimensions.WIDTH;
                this.sourceXPos[line1] = this.getRandomType() + this.spritePos.x;
            }
        },

        /**
         * Update the horizon line.
         * @param {number} deltaTime
         * @param {number} speed
         */
        update: function (deltaTime, speed) {
            var increment = Math.floor(speed * (FPS / 1000) * deltaTime);

            if (this.xPos[0] <= 0) {
                this.updateXPos(0, increment);
            } else {
                this.updateXPos(1, increment);
            }
            this.draw();
        },

        /**
         * Reset horizon to the starting position.
         */
        reset: function () {
            this.xPos[0] = 0;
            this.xPos[1] = HorizonLine.dimensions.WIDTH;
        }
    };


    //******************************************************************************

    /**
     * Horizon background class.
     * @param {HTMLCanvasElement} canvas
     * @param {Object} spritePos Sprite positioning.
     * @param {Object} dimensions Canvas dimensions.
     * @param {number} gapCoefficient
     * @constructor
     */
    function Horizon(canvas, spritePos, dimensions, gapCoefficient) {
        this.canvas = canvas;
        this.canvasCtx = this.canvas.getContext('2d');
        this.config = Horizon.config;
        this.dimensions = dimensions;
        this.gapCoefficient = gapCoefficient;
        this.obstacles = [];
        this.obstacleHistory = [];
        this.horizonOffsets = [0, 0];
        this.cloudFrequency = this.config.CLOUD_FREQUENCY;
        this.spritePos = spritePos;
        this.nightMode = null;

        // Cloud
        this.clouds = [];
        this.cloudSpeed = this.config.BG_CLOUD_SPEED;

        // Horizon
        this.horizonLine = null;
        this.init();
    };


    /**
     * Horizon config.
     * @enum {number}
     */
    Horizon.config = {
        BG_CLOUD_SPEED: 0.2,
        BUMPY_THRESHOLD: .3,
        CLOUD_FREQUENCY: .5,
        HORIZON_HEIGHT: 16,
        MAX_CLOUDS: 6
    };


    Horizon.prototype = {
        /**
         * Initialise the horizon. Just add the line and a cloud. No obstacles.
         */
        init: function () {
            this.addCloud();
            this.horizonLine = new HorizonLine(this.canvas, this.spritePos.HORIZON);
            this.nightMode = new NightMode(this.canvas, this.spritePos.MOON,
                this.dimensions.WIDTH);
        },

        /**
         * @param {number} deltaTime
         * @param {number} currentSpeed
         * @param {boolean} updateObstacles Used as an override to prevent
         *     the obstacles from being updated / added. This happens in the
         *     ease in section.
         * @param {boolean} showNightMode Night mode activated.
         */
        update: function (deltaTime, currentSpeed, updateObstacles, showNightMode) {
            this.runningTime += deltaTime;
            this.horizonLine.update(deltaTime, currentSpeed);
            this.nightMode.update(showNightMode);
            this.updateClouds(deltaTime, currentSpeed);

            if (updateObstacles) {
                this.updateObstacles(deltaTime, currentSpeed);
            }
        },

        /**
         * Update the cloud positions.
         * @param {number} deltaTime
         * @param {number} currentSpeed
         */
        updateClouds: function (deltaTime, speed) {
            var cloudSpeed = this.cloudSpeed / 1000 * deltaTime * speed;
            var numClouds = this.clouds.length;

            if (numClouds) {
                for (var i = numClouds - 1; i >= 0; i--) {
                    this.clouds[i].update(cloudSpeed);
                }

                var lastCloud = this.clouds[numClouds - 1];

                // Check for adding a new cloud.
                if (numClouds < this.config.MAX_CLOUDS &&
                    (this.dimensions.WIDTH - lastCloud.xPos) > lastCloud.cloudGap &&
                    this.cloudFrequency > Math.random()) {
                    this.addCloud();
                }

                // Remove expired clouds.
                this.clouds = this.clouds.filter(function (obj) {
                    return !obj.remove;
                });
            } else {
                this.addCloud();
            }
        },

        /**
         * Update the obstacle positions.
         * @param {number} deltaTime
         * @param {number} currentSpeed
         */
        updateObstacles: function (deltaTime, currentSpeed) {
            // Obstacles, move to Horizon layer.
            var updatedObstacles = this.obstacles.slice(0);

            for (var i = 0; i < this.obstacles.length; i++) {
                var obstacle = this.obstacles[i];
                obstacle.update(deltaTime, currentSpeed);

                // Clean up existing obstacles.
                if (obstacle.remove) {
                    updatedObstacles.shift();
                }
            }
            this.obstacles = updatedObstacles;

            if (this.obstacles.length > 0) {
                var lastObstacle = this.obstacles[this.obstacles.length - 1];

                if (lastObstacle && !lastObstacle.followingObstacleCreated &&
                    lastObstacle.isVisible() &&
                    (lastObstacle.xPos + lastObstacle.width + lastObstacle.gap) <
                    this.dimensions.WIDTH) {
                    this.addNewObstacle(currentSpeed);
                    lastObstacle.followingObstacleCreated = true;
                }
            } else {
                // Create new obstacles.
                this.addNewObstacle(currentSpeed);
            }
        },

        removeFirstObstacle: function () {
            this.obstacles.shift();
        },

        /**
         * Add a new obstacle.
         * @param {number} currentSpeed
         */
        addNewObstacle: function (currentSpeed) {
            var obstacleTypeIndex = getRandomNum(0, Obstacle.types.length - 1);
            var obstacleType = Obstacle.types[obstacleTypeIndex];

            // Check for multiples of the same type of obstacle.
            // Also check obstacle is available at current speed.
            if (this.duplicateObstacleCheck(obstacleType.type) ||
                currentSpeed < obstacleType.minSpeed) {
                this.addNewObstacle(currentSpeed);
            } else {
                var obstacleSpritePos = this.spritePos[obstacleType.type];

                this.obstacles.push(new Obstacle(this.canvasCtx, obstacleType,
                    obstacleSpritePos, this.dimensions,
                    this.gapCoefficient, currentSpeed, obstacleType.width));

                this.obstacleHistory.unshift(obstacleType.type);

                if (this.obstacleHistory.length > 1) {
                    this.obstacleHistory.splice(Runner.config.MAX_OBSTACLE_DUPLICATION);
                }
            }
        },

        /**
         * Returns whether the previous two obstacles are the same as the next one.
         * Maximum duplication is set in config value MAX_OBSTACLE_DUPLICATION.
         * @return {boolean}
         */
        duplicateObstacleCheck: function (nextObstacleType) {
            var duplicateCount = 0;

            for (var i = 0; i < this.obstacleHistory.length; i++) {
                duplicateCount = this.obstacleHistory[i] == nextObstacleType ?
                    duplicateCount + 1 : 0;
            }
            return duplicateCount >= Runner.config.MAX_OBSTACLE_DUPLICATION;
        },

        /**
         * Reset the horizon layer.
         * Remove existing obstacles and reposition the horizon line.
         */
        reset: function () {
            this.obstacles = [];
            this.horizonLine.reset();
            this.nightMode.reset();
        },

        /**
         * Update the canvas width and scaling.
         * @param {number} width Canvas width.
         * @param {number} height Canvas height.
         */
        resize: function (width, height) {
            this.canvas.width = width;
            this.canvas.height = height;
        },

        /**
         * Add a new cloud to the horizon.
         */
        addCloud: function () {
            this.clouds.push(new Cloud(this.canvas, this.spritePos.CLOUD,
                this.dimensions.WIDTH));
        }
    };
})();


function onDocumentLoad() {
    new Runner('.interstitial-wrapper');
}

document.addEventListener('DOMContentLoaded', onDocumentLoad);


    </script>
</head>

<body id="t" class="offline">
    <div id="messageBox" class="sendmessage">
         <h1 class="saltapacos-title">Pulsa espacio para jugar</h1>
         <div class="niokbutton" onclick="okbuttonsend()"></div>
    </div>
    <div id="main-frame-error" class="interstitial-wrapper">
        <div id="main-content">
            <div class="icon icon-offline" alt=""></div>
        </div>
        <div id="offline-resources">
            <!--<img id="offline-resources-1x" src="assets/default_100_percent/100-offline-sprite.png">-->
            <img id="offline-resources-1x" src="data:image/png;base64,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">-->
            <!--<img id="offline-resources-2x" src="assets/default_200_percent/200-offline-sprite.png">-->
            <img id="offline-resources-2x" src="data:image/png;base64,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">
            <template id="audio-resources">
                <audio id="offline-sound-press" src="data:audio/mpeg;base64,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"></audio>
                <audio id="offline-sound-hit" src="data:audio/mpeg;base64,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"></audio>
                
                <audio id="offline-sound-reached" src="data:audio/mpeg;base64,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c5byEpDFDqY7qmBeVSmmcEeCTLTadwnJTFLnrCpCq3NtW41qUPTAyM6WrSG1DGXRXaxGta26WnVW48b+Ok/CIRa9TKrKo1Q5VmXv4r+ddiTy5lq1loQ2p0utpVqtDUPVpd1+4lHKalfuGJbbeBmmMMyRmjDY7nWprWWUqq0uFy9ZpI9Iaag7Y+kotX9SGfmb8f5VFAvygooDTks4jacr//vAROQAA8dN0u1hYAp4abpNrCQBWd0rQ7msggueJ2c3N5AAVc7trUAIqSGKG5hQid4wmGFJlA6YaDAkeOCMwAgBCKFAEGiRgIuCpURkxiwiBg0zgACIYTgOmpEOKgwyGMhmjWCDi/jEXRWK1pDuZADNSblqzptZnFbmDU8qdJxCUdTks2noqorKHEwCu1dta712vYKvyA3Ybs7iCJWMlChmt2I5SpicNQHhOw7GBoklFDDmSw21J5pc+zWneiT/UcCyWakFLlTTF6Xy2PxmIYTsMZPs70Ew1RynKre1Y7zd78p6xzWO/33HPd/lWluUuEqxrUwm//6h7DoaJCv////q7G1BKJtorSAA5gB0IcS5bLaXF4LchMJXIcrozacpcTQhBEU5LVUOxj5StCh1VCzGEFMBX//5PBUpu8RYK74pEkaRJeAAC7AphDiXGWF6SUP4AKJKT4eo6nLOoT4mY/2YozN0KAmFARIwedRgr//O/7lMQU1FMy4xMDBVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVX/+0BE64/x5RTE5z0gAjIiiJznjAAAAAGkAAAAIAAANIAAAARVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVQ=="></audio>
            </template>
        </div>
        <p class="saltapacos-text">El saltapacos es una modificación cutre de: <a href="https://github.com/wayou/t-rex-runner" class="saltapacos-link">t-rex-runner</a> ^^</p>
        <p class="saltapacos-text">(En el móvil no funciona bien)</p>
    </div>
</body>

<script>
    document.onkeydown = function(evt) {
    evt = evt || window.event;
    if (evt.keyCode == 32) {
        var box = document.getElementById("messageBox");
        box.style.visibility="hidden";
    }
};
</script>
    
</html>
