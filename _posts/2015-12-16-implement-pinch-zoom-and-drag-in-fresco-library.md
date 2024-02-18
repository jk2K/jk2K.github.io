---
title: 让 fresco 支持双指缩放与拖动
date: 2015-12-16 17:37:02 +0800
categories: [技术]
tags:
- android
---
## 需求
fresco 的 SimpleDraweeView 不支持缩放与拖动，想尽可能简单的实现这个功能

## 代码
ZoomableDraweeView.java

```java
package com.xmeise.mm;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Matrix;
import android.support.annotation.NonNull;
import android.util.AttributeSet;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.ScaleGestureDetector;

import com.facebook.drawee.view.SimpleDraweeView;

/*
 * The MIT License (MIT)
 *
 * Copyright (c) 2015 - Nathan Barraille, jk2K
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */
public class ZoomableDraweeView extends SimpleDraweeView {
    private ScaleGestureDetector mScaleDetector;
    private GestureDetector mGestureDetector;

    private float mCurrentScale = 1.0f;
    private Matrix mCurrentMatrix;
    private float mMidX;
    private float mMidY;
    private OnClickListener mClickListener;
    private OnLongPressListener mLongPressListener;

    public ZoomableDraweeView(Context context) {
        this(context, null);
    }

    public ZoomableDraweeView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public ZoomableDraweeView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);

        init();
    }

    private void init() {
        ScaleGestureDetector.OnScaleGestureListener mScaleListener = new ScaleGestureDetector.SimpleOnScaleGestureListener() {
            @Override
            public boolean onScale(ScaleGestureDetector detector) {
                float scaleFactor = detector.getScaleFactor();
                float newScale = mCurrentScale * scaleFactor;
                // Prevent from zooming out more than original
                if (newScale > 1.0f) {
                    // We initialize this lazily so that we don't have to register (and force the user
                    // to unregister) a global layout listener on the view.
                    if (mMidX == 0.0f) {
                        mMidX = getWidth() / 2.0f;
                    }
                    if (mMidY == 0.0f) {
                        mMidY = getHeight() / 2.0f;
                    }
                    mCurrentScale = newScale;
                    // support pinch zoom
                    mCurrentMatrix.postScale(scaleFactor, scaleFactor, mMidX, mMidY);
                    invalidate();
                } else {
                    scaleFactor = 1.0f / mCurrentScale;
                    reset();
                }

                return true;
            }
        };
        mScaleDetector = new ScaleGestureDetector(getContext(), mScaleListener);
        mCurrentMatrix = new Matrix();

        GestureDetector.SimpleOnGestureListener mGestureListener = new GestureDetector.SimpleOnGestureListener() {
            public void onLongPress (MotionEvent e) {
                // support long press listener
                if (mLongPressListener != null) {
                    mLongPressListener.onLongPress();
                }
            }

            public boolean onSingleTapConfirmed (MotionEvent e) {
                // support single tap listener
                if (mClickListener != null) {
                    mClickListener.onClick();
                }
                return true;
            }

            public boolean onScroll (MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
                // support drag
                // disable drag when normal scale
                if (mCurrentScale > 1.0f) {
                    mCurrentMatrix.postTranslate(-distanceX, -distanceY);
                    invalidate();
                }
                return true;
            }
        };
        mGestureDetector = new GestureDetector(getContext(), mGestureListener);
    }

    @Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
    }

    @Override
    protected void onDraw(@NonNull Canvas canvas) {
        int saveCount = canvas.save();
        canvas.concat(mCurrentMatrix);
        super.onDraw(canvas);
        canvas.restoreToCount(saveCount);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        mScaleDetector.onTouchEvent(event);
        if (!mScaleDetector.isInProgress()) {
            mGestureDetector.onTouchEvent(event);
        }

        return true;
    }

    /**
     * Resets the zoom of the attached image.
     * This has no effect if the image has been destroyed
     */
    public void reset() {
        mCurrentMatrix.reset();
        mCurrentScale = 1.0f;
        invalidate();
    }

    public void setOnLongPressListener(OnLongPressListener listener) {
        mLongPressListener = listener;
    }

    public interface OnLongPressListener {
        void onLongPress();
    }

    public void setOnClickListener(OnClickListener listener) {
        mClickListener = listener;
    }

    public interface OnClickListener {
        void onClick();
    }
}
```

## 说明
在 [PinchToZoomScaleDetector.java](https://gist.github.com/nbarraille/eb5d0da20bc813969b08) 的基础上实现了拖动操作, 不是很难, 在这个过程中最大的收获就是了解了`ScaleGestureDetector`和`GestureDetector`该如何使用, 好的, 尽情使用吧


## 参考链接
* [Implementing pinch zoom and drag using Android&#039;s build in gesture listener and scale listener](http://stackoverflow.com/questions/19418878/implementing-pinch-zoom-and-drag-using-androids-build-in-gesture-listener-and-s/19545542#19545542)
* [ZoomableDraweeView-sample](https://github.com/kunny/ZoomableDraweeView-sample)
* [PinchToZoomScaleDetector.java](https://gist.github.com/nbarraille/eb5d0da20bc813969b08)
* [fresco docs](http://frescolib.org/)
