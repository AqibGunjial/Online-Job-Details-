import React, { useEffect, useState } from 'react';
import {
    View,
    Text,
    TextInput,
    Button,
    FlatList,
    TouchableOpacity,
    ActivityIndicator,
    Alert,
    StyleSheet
} from 'react-native';
import axios from 'axios';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

// 🔹 Login Screen
const LoginScreen = ({ navigation }) => {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');

    const handleLogin = () => {
        if (email === 'aqibgunjial00@gmail.com' && password === 'Aqib123') {
            navigation.navigate('JobList');
        } else {
            Alert.alert('Login Failed', 'Invalid email or password');
        }
    };

    return (
        <View style={styles.container}>
            <Text style={styles.title}>Login</Text>
            <TextInput
                placeholder="Email"
                value={email}
                onChangeText={setEmail}
                style={styles.input}
                keyboardType="email-address"
                autoCapitalize="none"
            />
            <TextInput
                placeholder="Password"
                value={password}
                onChangeText={setPassword}
                style={styles.input}
                secureTextEntry
            />
            <Button title="Login" onPress={handleLogin} />
        </View>
    );
};

// 🔹 Job List Screen (API or fallback to static)
const JobListScreen = ({ navigation }) => {
    const [jobs, setJobs] = useState([]);
    const [loading, setLoading] = useState(true);

    const staticJobs = [
        { title: 'Software Engineer', description: 'Develop applications.', requirements: '3 years experience', location: 'New York', apply_link: '#' },
        { title: 'Product Manager', description: 'Manage products.', requirements: '5 years experience', location: 'San Francisco', apply_link: '#' },
        { title: 'Data Scientist', description: 'Analyze data.', requirements: '2 years experience', location: 'Chicago', apply_link: '#' },
        { title: 'Web Developer', description: 'Build websites.', requirements: '3 years experience', location: 'LA', apply_link: '#' },
        { title: 'UX Designer', description: 'Design interfaces.', requirements: '2 years experience', location: 'Austin', apply_link: '#' },
        { title: 'Project Manager', description: 'Lead teams.', requirements: '5 years experience', location: 'Seattle', apply_link: '#' },
        { title: 'ML Engineer', description: 'Develop ML models.', requirements: '3 years experience', location: 'SF', apply_link: '#' },
        { title: 'Mobile Developer', description: 'Build apps.', requirements: '4 years experience', location: 'Miami', apply_link: '#' },
        { title: 'DBA', description: 'Manage databases.', requirements: '3 years experience', location: 'NY', apply_link: '#' },
        { title: 'DevOps Engineer', description: 'Manage infrastructure.', requirements: '5 years experience', location: 'Denver', apply_link: '#' }
    ];

    useEffect(() => {
        axios.get('http://192.168.185.103:5000/jobs') // Change to your IP or API URL
            .then(response => {
                setJobs(response.data);
                setLoading(false);
            })
            .catch(error => {
                console.warn('API failed, using static jobs.');
                setJobs(staticJobs);
                setLoading(false);
            });
    }, []);

    return (
        <View style={{ flex: 1, padding: 20 }}>
            <Text style={styles.title}>Job Listings</Text>
            {loading ? (
                <ActivityIndicator size="large" color="blue" />
            ) : (
                <FlatList
                    data={jobs}
                    keyExtractor={(item, index) => index.toString()}
                    renderItem={({ item }) => (
                        <TouchableOpacity onPress={() => navigation.navigate('JobDetails', { job: item })}>
                            <View style={styles.jobItem}>
                                <Text style={styles.jobTitle}>{item.title}</Text>
                                <Text style={styles.jobLocation}>{item.location}</Text>
                            </View>
                        </TouchableOpacity>
                    )}
                />
            )}
        </View>
    );
};

// 🔹 Job Details Screen
const JobDetailsScreen = ({ route }) => {
    const { job } = route.params;

    return (
        <View style={{ flex: 1, padding: 20 }}>
            <Text style={styles.jobTitle}>{job.title}</Text>
            <Text style={styles.jobDescription}>{job.description}</Text>
            <Text style={styles.jobText}>Requirements: {job.requirements}</Text>
            <Text style={styles.jobText}>Location: {job.location}</Text>
            <Text style={[styles.jobText, { color: 'blue' }]}>Apply Here: {job.apply_link}</Text>
        </View>
    );
};

// 🔹 App Entry Point
export default function App() {
    return (
        <NavigationContainer>
            <Stack.Navigator initialRouteName="Login">
                <Stack.Screen name="Login" component={LoginScreen} />
                <Stack.Screen name="JobList" component={JobListScreen} />
                <Stack.Screen name="JobDetails" component={JobDetailsScreen} />
            </Stack.Navigator>
        </NavigationContainer>
    );
}

// 🔹 Styles
const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        padding: 20,
        backgroundColor: '#fff',
    },
    title: {
        fontSize: 24,
        fontWeight: 'bold',
        marginBottom: 20,
        textAlign: 'center'
    },
    input: {
        width: '100%',
        borderWidth: 1,
        borderColor: '#ccc',
        padding: 10,
        borderRadius: 8,
        marginBottom: 15,
    },
    jobItem: {
        padding: 12,
        borderBottomWidth: 1,
        borderColor: '#ddd'
    },
    jobTitle: {
        fontSize: 18,
        fontWeight: 'bold',
    },
    jobLocation: {
        fontSize: 14,
        color: 'gray',
    },
    jobDescription: {
        fontSize: 16,
        marginTop: 10,
    },
    jobText: {
        fontSize: 16,
        marginTop: 10,
    },
});
