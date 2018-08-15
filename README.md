# Android-Volley-Multipart-Request
Android Multiple Images upload Class

Use this code for Sending the MultiPart Request in your activity

 String url = Constants.base_Url +"Users/add_listing";

        VolleyMultipartRequest multipartRequest = new VolleyMultipartRequest(Request.Method.POST, url, new     Response.Listener<NetworkResponse>() {
            @Override
            public void onResponse(NetworkResponse response) {
            
            //Do your logic here 
            
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                progressDialog.hide();

                Log.v("Error", "" + error);
                Toast.makeText(getApplicationContext(), "" + error, Toast.LENGTH_LONG).show();

                NetworkResponse networkResponse = error.networkResponse;
                String errorMessage = "Unknown error";
                if (networkResponse == null) {
                    if (error.getClass().equals(TimeoutError.class)) {
                        errorMessage = "Request timeout";
                    } else if (error.getClass().equals(NoConnectionError.class)) {
                        errorMessage = "Failed to connect server";
                    }
                } else {
                    String result = new String(networkResponse.data);
                    try {
                        JSONObject response = new JSONObject(result);
                        String status = response.getString("status");
                        String message = response.getString("message");

                        Log.e("Error Status", status);
                        Log.e("Error Message", message);

                        if (networkResponse.statusCode == 404) {
                            errorMessage = "Resource not found";
                        } else if (networkResponse.statusCode == 401) {
                            errorMessage = message + " Please login again";
                        } else if (networkResponse.statusCode == 400) {
                            errorMessage = message + " Check your inputs";
                        } else if (networkResponse.statusCode == 500) {
                            errorMessage = message + " Something is getting wrong";
                        }
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
                }
                Log.i("Error", errorMessage);
                error.printStackTrace();
            }
        }) {{
            @Override
            protected Map<String, String> getParams() {
                Map<String, String> params = new HashMap<>();
                params.put("params", Param_Variable);
                return params;
            }

            @Override
            protected Map<String, DataPart> getByteData() {
                Map<String, DataPart> params = new HashMap<>();
                // file name could found file base or direct access from real path
                //for now just get bitmap data from ImageView

                File uploads[] = new File[images.size()];
                for (int i = 0; i < images.size(); i++) {

                    try {
                       
                        params.put("uploads[" + i + "]", new DataPart(FileName + i + ".jpg", b, "image/*"));

                    } catch (Exception e) {
                        e.printStackTrace();
                    }


                }

                return params;
            }
        };

        MySingleton.getInstance(getBaseContext()).addToRequestQueue(multipartRequest);
