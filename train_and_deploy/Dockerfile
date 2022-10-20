FROM paddlepaddle/paddle:2.3.2-gpu-cuda10.2-cudnn7
ENV LANG=en_US.utf8
ENV LANG=C.UTF-8

ENV PYTHONUNBUFFERED=TRUE
ENV PYTHONDONTWRITEBYTECODE=TRUE
ENV PATH="/opt/program:${PATH}"

##########################################################################################
# SageMaker requirements
##########################################################################################
RUN pip3 install --upgrade pip
RUN pip3 install networkx==2.3 flask gevent gunicorn boto3 paddleocr==2.6.0
RUN pip3 install paddlepaddle-gpu -i https://mirror.baidu.com/pypi/simple
RUN pip3 install paddlenlp

# Add folder
COPY PaddleOCROriginal/ /opt/program/

# Download pretrained model for finetunine
RUN mkdir /opt/program/pretrain/
RUN cd /opt/program/pretrain/
RUN wget -P /opt/program/pretrain/ https://paddleocr.bj.bcebos.com/ppstructure/models/vi_layoutxlm/ser_vi_layoutxlm_xfund_pretrained.tar && tar -xf /opt/program/pretrain/ser_vi_layoutxlm_xfund_pretrained.tar -C /opt/program/pretrain/ && rm -rf /opt/program/pretrain/ser_vi_layoutxlm_xfund_pretrained.tar

# Download model for inference
RUN mkdir /opt/program/inference/
RUN cd /opt/program/inference/
#RUN wget -P /opt/program/inference/ https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_det_infer.tar && tar -xf /opt/program/inference/ch_ppocr_server_v2.0_det_infer.tar -C /opt/program/inference/ && rm -rf /opt/program/inference/ch_ppocr_server_v2.0_det_infer.tar
#RUN wget -P /opt/program/inference/ https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_mobile_v2.0_cls_infer.tar && tar -xf /opt/program/inference/ch_ppocr_mobile_v2.0_cls_infer.tar -C /opt/program/inference/ && rm -rf /opt/program/inference/ch_ppocr_mobile_v2.0_cls_infer.tar
#RUN wget -P /opt/program/inference/ https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_rec_infer.tar && tar -xf /opt/program/inference/ch_ppocr_server_v2.0_rec_infer.tar -C /opt/program/inference/ && rm -rf /opt/program/inference/ch_ppocr_server_v2.0_rec_infer.tar
RUN wget -P /opt/program/inference/ https://paddleocr.bj.bcebos.com/ppstructure/models/vi_layoutxlm/ser_vi_layoutxlm_xfund_infer.tar && tar -xf /opt/program/inference/ser_vi_layoutxlm_xfund_infer.tar -C /opt/program/inference/ && rm -rf /opt/program/inference/ser_vi_layoutxlm_xfund_infer.tar

#RUN pip3.7 install -r /opt/program/requirements.txt -i https://opentuna.cn/pypi/web/simple

### Install nginx notebook
RUN apt-get -y update && apt-get install -y --no-install-recommends \
         wget \
         nginx \
         ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log

# Set up the program in the image
COPY train-and-deploy-ocr-model-on-amazon-sagemaker/train_and_deploy/paddle/* /opt/program/
RUN chmod +x /opt/program/serve
RUN chmod +x /opt/program/train
WORKDIR /opt/program