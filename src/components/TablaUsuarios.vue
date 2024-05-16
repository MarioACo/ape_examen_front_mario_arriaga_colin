<template>
    <div class="card">
        <table>
            <thead>
                <tr>
                    <th>Nombre de usuario</th>
                    <th>Fecha de nacimiento</th>
                    <th>Edad</th>
                    <th>Direccion</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in paginatedUsers" :key="user.id">
                    <td>{{ user.nombre }}</td>
                    <td>{{ user.fecha_nacimiento }}</td>
                    <td>{{ user.edad }}</td>
                    <td>{{ getDirection(user.domicilio) }}</td>
                </tr>
            </tbody>
        </table>
    </div>
    <div class="paginator">
        <button @click="previousPage" :disabled="currentPage === 1" class="page-button">Anterior</button>
        <button @click="nextPage" :disabled="currentPage === totalPages" class="page-button">Siguiente</button>
    </div>
</template>

<script>
    export default 
    {
        data() 
        {
            return {
                users: [],
                currentPage: 1,
                usersPerPage: 10,
            }
        },
        async mounted()
        {

            await this.fetchUsers();
            

        },
        methods:
        {

            async fetchUsers()
            {
                try
                {
                    const response = await fetch('http://localhost:8000/users', {method: 'GET'});

                    if ( !response.ok )
                    {
                        throw new Error('Error al obtener users');
                    }

                    const data = await response.json();

                    this.users = data;

                }catch ( error )
                {
                    console.error(error);
                }
            },

            nextPage()
            {
                if(this.currentPage < this.totalPages)
                    this.currentPage++;
            },
            previousPage()
            {
                if(this.currentPage > 1)
                    this.currentPage--;
            },
            getPaginatedUsers()
            {
                const startIndex = (this.currentPage - 1) * this.usersPerPage;
                const endIndex = startIndex + this.usersPerPage;
                return this.users.slice(startIndex, endIndex)
            },
            getDirection(address)
            {
                return address.map(add => `${add.domicilio}, ${add.numero_exterior}, ${add.colonia}, ${add.cp}, ${add.ciudad}`).join(';');
            }
        } ,
        computed:
        {
            totalPages()
            {
                return Math.ceil(this.users.length / this.usersPerPage)
            },
            paginatedUsers()
            {
                return this.getPaginatedUsers();
            }
        }
    }
    

</script>

<style scoped>
.card
{
    background-color: #f4f4f4;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    padding: 20px;
    width: 80%;
    max-width: 800px;
    margin: 0 auto;
}

table
{
    width: 100%;
    border-collapse: collapse;
    border-spacing: 0;
}

table th, table td
{
    padding: 12px 15px;
    text-align: left;
    border-bottom: 1px solid #ddd;
}

table th
{
    background-color: #f2f2f2;
}

table tbody tr:nth-of-type(even)
{
    background-color: #f9f9f9;
}

table tbody tr:lat-of-type
{
    border-bottom: 2px solid #ddd
}

.paginator
{
    text-align: center;
    margin-top: 20px;
}

.page-button
{
    padding: 8px 16px;
    margin: 0 50px;
    background-color: #007BFF;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.page-button:hover
{
    background-color: #0056B3;
}

.page-button:disabled
{
    background: #ccc;
    cursor: not-allowed
}

</style>