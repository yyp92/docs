# react svga 组件封装

移动端支持动画的图片文件，因为svga是文件，所以不能直接使用img标签加载。

```js
import React from 'react';
import { isFunction } from 'lodash';
import * as SVGA from 'svgaplayerweb';
import s from './index.module.scss';

interface SvgaImgProps {
  src?: string;
  id?: string;
  placeholder?: React.ReactNode;
  goPage?: () => void;

  [key: string]: any;
}

const SvgaImg: React.FC<SvgaImgProps> = ({
    src = '',
    id = '',
    placeholder = null,
    goPage
}) => {
  const [isSuccess, setIsSuccess] = React.useState<boolean>(false);

  React.useEffect(() => {
    const player = new SVGA.Player(`#svga-${id}`);
    const parser = new SVGA.Parser();

    // 跨域的 SVGA 资源需要使用 CORS 协议才能加载成功。
    parser.load(src, videoItem => {
      setIsSuccess(true);
      player.setVideoItem(videoItem);
      player.startAnimation();
    });

    return () => {
      player.clear();
    };
  }, []);

  const renderContent = () => {
    if (isSuccess) {
      return null;
    }

    return <div className={s.placeholder}>{placeholder}</div>;
  };

  const renderWrap = () => {
    return (
      <div className={`${s.svgaWrap} svga-wrap`}>
        {renderContent()}

        <div
            id={`svga-${id}`}
            onClick={() => isSuccess && isFunction(goPage) && goPage()}
        />
      </div>
    );
  };

  return renderWrap();
};

export default SvgaImg;
```

```scss
.svgaWrap {
    width: 100%;
    height: 100%;
    position: relative;

    canvas {
        transform: none !important;
    }

    .placeholder {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
        left: 0;
    }
}
```
