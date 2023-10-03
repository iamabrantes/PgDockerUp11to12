FROM postgres:11.21-bullseye AS pg11

FROM postgres:12.16-bullseye

COPY --from=pg11 /usr/lib/postgresql/11 /usr/lib/postgresql/11
COPY --from=pg11 /usr/share/postgresql/11 /usr/share/postgresql/11

USER postgres

WORKDIR /home/
CMD ["bash","-c","sleep 6000000"]