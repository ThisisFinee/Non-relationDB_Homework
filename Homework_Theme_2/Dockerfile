FROM mongo:6.0

COPY listingsAndReviews.json /tmp/

RUN echo '#!/bin/bash\n' \
         'echo "Ожидание запуска MongoDB..."\n' \
         'until mongosh --eval "print(\"waited for connection\")" 2>/dev/null; do\n' \
         '  sleep 1\n' \
         'done\n' \
         'echo "Импорт данных..."\n' \
         'mongoimport --db sample_airbnb --collection listingsAndReviews --file /tmp/listingsAndReviews.json\n' \
         'echo "Импорт завершен. Загружено документов:"\n' \
         'mongosh --eval "db = db.getSiblingDB(\"sample_airbnb\"); print(db.listingsAndReviews.countDocuments())"\n' \
         > /docker-entrypoint-initdb.d/init.sh

RUN chmod +x /docker-entrypoint-initdb.d/init.sh

EXPOSE 27017

CMD ["mongod", "--bind_ip_all"]