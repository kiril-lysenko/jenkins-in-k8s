kubectl -n ${NAMESPACE} describe secret $(kubectl -n ${NAMESPACE} get secret | (grep demo-sa-secret || echo "$_") | awk '{print $1}') | grep token: | awk '{print $2}'\n
