FROM python:3.12-alpine
WORKDIR /content/root
VOLUME ["/content/root"]
COPY . /content/root
EXPOSE 80
CMD ["python3", "-m", "http.server", "--bind", "0.0.0.0", "80"]