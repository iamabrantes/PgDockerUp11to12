FROM postgres:12.16-bullseye AS pg12

FROM postgres:13.18-bullseye

COPY --from=pg12 /usr/lib/postgresql/12 /usr/lib/postgresql/12
COPY --from=pg12 /usr/share/postgresql/12 /usr/share/postgresql/12

USER postgres

WORKDIR /home/
CMD ["bash","-c","sleep 6000000"]