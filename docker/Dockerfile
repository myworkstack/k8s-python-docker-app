FROM python:3.7

RUN mkdir /myworkspace
RUN cd myworkspace
WORKDIR /myworkspace
ADD hello.py .
RUN pip3 install flask

EXPOSE 5000
CMD ["python", "/myworkspace/hello.py"]